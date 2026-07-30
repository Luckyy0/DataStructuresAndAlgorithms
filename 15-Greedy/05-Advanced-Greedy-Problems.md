# Advanced Greedy & Math Problems

Tài liệu này bao gồm 30 bài tập về Advanced Greedy và Math, trong đó 10 bài đầu tiên được cung cấp đầy đủ đề bài, phân tích, mã nguồn Java và phân tích độ phức tạp. 20 bài sau được tóm tắt ý tưởng.

---

## 1. Maximum Swap (LeetCode 670)
**Đề bài:** Cho một số nguyên không âm `num`. Bạn có thể hoán đổi hai chữ số nhiều nhất một lần để có được số lớn nhất có thể. Trả về giá trị tối đa bạn có thể nhận được.

**Phân tích thuật toán:**
- Dùng Greedy kết hợp với mảng đánh dấu (Bucket).
- Ghi lại vị trí (index) xuất hiện cuối cùng của từng chữ số từ 0 đến 9.
- Duyệt từng chữ số của `num` từ trái qua phải. Với mỗi chữ số, kiểm tra xem có chữ số nào lớn hơn nó (từ 9 lùi về `chữ số hiện tại + 1`) và xuất hiện ở vị trí phía sau không.
- Nếu có, tiến hành hoán đổi ngay lập tức và trả về kết quả vì việc đổi chữ số lớn nhất ở vị trí có trọng số cao nhất (trái nhất) sẽ mang lại lợi ích lớn nhất.

**Mã nguồn Java:**
```java
class Solution {
    public int maximumSwap(int num) {
        char[] digits = Integer.toString(num).toCharArray();
        int[] last = new int[10];
        
        // Ghi nhận vị trí xuất hiện cuối cùng của mỗi chữ số
        for (int i = 0; i < digits.length; i++) {
            last[digits[i] - '0'] = i;
        }
        
        // Duyệt từ trái sang phải
        for (int i = 0; i < digits.length; i++) {
            // Tìm xem có chữ số lớn hơn ở bên phải không
            for (int d = 9; d > digits[i] - '0'; d--) {
                if (last[d] > i) {
                    // Hoán đổi
                    char temp = digits[i];
                    digits[i] = digits[last[d]];
                    digits[last[d]] = temp;
                    return Integer.parseInt(new String(digits));
                }
            }
        }
        
        return num;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N)$ với $N$ là số lượng chữ số của `num` (tối đa khoảng 9 chữ số cho `int`).
- Space: $\mathcal{O}(N)$ để lưu mảng `char[]` và mảng đếm tần suất kích thước hằng số 10.

---

## 2. Wiggle Subsequence (LeetCode 376)
**Đề bài:** Một chuỗi wiggle là chuỗi mà các chênh lệch giữa các số kề nhau luân phiên đổi dấu (dương, âm). Trả về độ dài chuỗi con (subsequence) lớn nhất là chuỗi wiggle.

**Phân tích thuật toán:**
- Tư duy tham lam: Chúng ta chỉ quan tâm đến các đỉnh và đáy (local maxima and minima) của mảng. Các điểm trung gian trong một đoạn đơn điệu tăng hoặc giảm sẽ không giúp chuỗi wiggle dài thêm.
- Ta duy trì hai biến `up` và `down`. `up` là độ dài chuỗi kết thúc bởi một bước tăng, `down` là độ dài kết thúc bởi bước giảm.

**Mã nguồn Java:**
```java
class Solution {
    public int wiggleMaxLength(int[] nums) {
        if (nums.length < 2) return nums.length;
        
        int up = 1, down = 1;
        
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] > nums[i - 1]) {
                up = down + 1; // Nhận một đỉnh (local maxima)
            } else if (nums[i] < nums[i - 1]) {
                down = up + 1; // Nhận một đáy (local minima)
            }
        }
        
        return Math.max(up, down);
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N)$ do chỉ duyệt mảng một lần.
- Space: $\mathcal{O}(1)$.

---

## 3. Task Scheduler (LeetCode 621)
**Đề bài:** Cho danh sách các `tasks` (ký tự A-Z) và một số nguyên `n` đại diện cho thời gian cooldown. Khoảng cách giữa 2 task cùng loại phải là ít nhất `n` đơn vị thời gian. Tìm thời gian tối thiểu để hoàn thành tất cả các task.

**Phân tích thuật toán:**
- Task xuất hiện nhiều nhất sẽ là "nút thắt cổ chai" (bottleneck).
- Giả sử tần suất task lớn nhất là $F$ và có $C$ task có cùng tần suất $F$.
- Chúng ta sẽ xếp task nhiều nhất vào khung thời gian, tạo ra $(F - 1)$ khoảng trống (chunks). Mỗi khoảng trống có độ dài tối thiểu là `n`.
- Ta có thể nhét các task khác vào các khoảng trống này. Nếu vượt quá không gian, độ dài thực tế sẽ chính là số lượng toàn bộ tasks.
- Công thức Greedy: `Math.max(tasks.length, (F - 1) * (n + 1) + C)`

**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int leastInterval(char[] tasks, int n) {
        int[] counts = new int[26];
        for (char c : tasks) {
            counts[c - 'A']++;
        }
        
        Arrays.sort(counts);
        int maxFreq = counts[25];
        
        int numMaxTasks = 0;
        for (int i = 25; i >= 0; i--) {
            if (counts[i] == maxFreq) {
                numMaxTasks++;
            } else {
                break;
            }
        }
        
        int time = (maxFreq - 1) * (n + 1) + numMaxTasks;
        
        return Math.max(time, tasks.length);
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N)$ duyệt toàn bộ mảng tasks, sắp xếp mảng hằng số kích thước 26.
- Space: $\mathcal{O}(1)$ mảng kích thước 26.

---

## 4. Maximum Number of Events That Can Be Attended (LeetCode 1353)
**Đề bài:** Cho mảng `events` với `events[i] = [startDay_i, endDay_i]`. Mỗi ngày bạn chỉ có thể dự 1 event. Tìm số lượng sự kiện tối đa bạn có thể tham dự.

**Phân tích thuật toán:**
- Sort các `events` theo `startDay`.
- Dùng một Min-Heap để lưu `endDay` của các events đang diễn ra.
- Duyệt qua từng ngày (day = 1, 2, ...). 
- Đưa tất cả event có `startDay == day` vào Heap. 
- Loại bỏ khỏi Heap những event có `endDay < day` (đã quá hạn).
- Tham lam chọn sự kiện có `endDay` gần nhất (đỉnh Heap) để tham gia trong ngày hôm đó.

**Mã nguồn Java:**
```java
import java.util.Arrays;
import java.util.PriorityQueue;

class Solution {
    public int maxEvents(int[][] events) {
        Arrays.sort(events, (a, b) -> Integer.compare(a[0], b[0]));
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        
        int i = 0;
        int n = events.length;
        int res = 0;
        int day = 0;
        
        while (i < n || !pq.isEmpty()) {
            // Nếu PQ rỗng và sự kiện tiếp theo ở tương lai xa, nhảy luôn tới ngày đó
            if (pq.isEmpty() && i < n) {
                day = events[i][0];
            }
            
            // Đẩy tất cả sự kiện bắt đầu vào ngày 'day' vào PQ
            while (i < n && events[i][0] <= day) {
                pq.offer(events[i][1]);
                i++;
            }
            
            // Bỏ các sự kiện đã kết thúc trước ngày 'day'
            while (!pq.isEmpty() && pq.peek() < day) {
                pq.poll();
            }
            
            // Chọn sự kiện kết thúc sớm nhất để tham gia
            if (!pq.isEmpty()) {
                pq.poll();
                res++;
            }
            day++;
        }
        
        return res;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N \log N)$ cho thao tác sort và push/pop Priority Queue.
- Space: $\mathcal{O}(N)$ lưu Priority Queue.

---

## 5. Course Schedule III (LeetCode 630)
**Đề bài:** Có $n$ khóa học, khóa học thứ $i$ có độ dài `duration_i` và cần hoàn thành trước hoặc vào ngày `lastDay_i`. Tìm số khóa học tối đa bạn có thể hoàn thành, bạn chỉ được học từng khóa một bắt đầu từ ngày 1.

**Phân tích thuật toán:**
- **Regret Greedy**: Sắp xếp các khóa học ưu tiên theo `lastDay` tăng dần (deadlines đến sớm thì xét trước).
- Duyệt các khóa học, cộng dồn `duration` vào `time`. Thêm khóa học vào một Max-Heap.
- Nếu `time > lastDay` (vi phạm), ta sẽ hối hận: lấy khóa học tốn nhiều thời gian nhất (trên đỉnh Max-Heap) ra và trừ thời gian của nó đi. Do `lastDay` hiện tại đang lớn hơn hoặc bằng các khóa trước, vứt khóa dài nhất đi chắc chắn giải phóng nhiều thời gian nhất mà vẫn hợp lệ.

**Mã nguồn Java:**
```java
import java.util.Arrays;
import java.util.PriorityQueue;

class Solution {
    public int scheduleCourse(int[][] courses) {
        // Sort by deadline
        Arrays.sort(courses, (a, b) -> a[1] - b[1]);
        
        // Max-heap for durations
        PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
        int time = 0;
        
        for (int[] c : courses) {
            time += c[0];
            pq.offer(c[0]);
            
            // Regret: if time exceeds deadline, remove the course with longest duration
            if (time > c[1]) {
                time -= pq.poll();
            }
        }
        
        return pq.size();
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N \log N)$ cho sắp xếp và PQ operations.
- Space: $\mathcal{O}(N)$ cho Max-Heap.

---

## 6. IPO (LeetCode 502)
**Đề bài:** Có $N$ dự án, dự án thứ $i$ yêu cầu vốn khởi điểm `Capital[i]` và mang lại lợi nhuận `Profits[i]`. Bạn bắt đầu với vốn ban đầu `W`, và được phép hoàn thành tối đa `k` dự án. Lợi nhuận kiếm được sẽ cộng dồn vào `W`. Tìm vốn tối đa sau cùng.

**Phân tích thuật toán:**
- Sử dụng **2 Heaps**. Một Min-Heap (hoặc mảng đã sort) lưu trữ các dự án dựa trên số vốn (Capital). Một Max-Heap lưu trữ lợi nhuận (Profit) của các dự án hiện tại đủ vốn để thực hiện.
- Tại mỗi bước (trong tối đa `k` bước), lấy tất cả các dự án có Capital $\le$ `W` chuyển từ Min-Heap sang Max-Heap.
- Rút dự án có Profit cao nhất từ Max-Heap thực hiện, cộng lãi vào `W`.

**Mã nguồn Java:**
```java
import java.util.Arrays;
import java.util.PriorityQueue;

class Solution {
    class Project {
        int capital, profit;
        public Project(int c, int p) {
            this.capital = c;
            this.profit = p;
        }
    }
    
    public int findMaximizedCapital(int k, int w, int[] profits, int[] capital) {
        int n = profits.length;
        Project[] projects = new Project[n];
        for (int i = 0; i < n; i++) {
            projects[i] = new Project(capital[i], profits[i]);
        }
        
        // Sort theo capital tăng dần
        Arrays.sort(projects, (a, b) -> a.capital - b.capital);
        
        // Max-Heap theo profit
        PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
        
        int ptr = 0;
        for (int i = 0; i < k; i++) {
            while (ptr < n && projects[ptr].capital <= w) {
                pq.offer(projects[ptr].profit);
                ptr++;
            }
            
            if (pq.isEmpty()) {
                break;
            }
            w += pq.poll();
        }
        
        return w;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N \log N)$ sắp xếp, $K \log N$ rút khỏi Max-Heap.
- Space: $\mathcal{O}(N)$ mảng object và Priority Queue.

---

## 7. Minimum Cost to Hire K Workers (LeetCode 857)
**Đề bài:** Có $N$ công nhân, công nhân thứ $i$ có `quality_i` và cần ít nhất `wage_i`. Yêu cầu thuê $K$ người. Tỷ lệ trả lương trên mỗi đơn vị quality của mọi người trong nhóm phải bằng nhau. Ai cũng phải nhận được $\ge$ `wage_i`. Tìm tổng chi phí thuê tối thiểu.

**Phân tích thuật toán:**
- Tỷ lệ `ratio = wage / quality`. Để mọi người thỏa mãn mức lương tối thiểu, tỷ lệ của nhóm phải bằng `ratio` lớn nhất của một worker bất kỳ được chọn trong nhóm đó.
- Nếu chọn một người có `ratio` làm gốc, chi phí thuê nhóm = `ratio * (tổng quality của K người)`.
- Sắp xếp workers theo `ratio` tăng dần. Khi duyệt, cập nhật tổng `quality` bằng cách thêm người hiện tại. Nếu số lượng $> K$, dùng Max-Heap đẩy người có `quality` lớn nhất ra để giảm thiểu tổng chi phí.

**Mã nguồn Java:**
```java
import java.util.Arrays;
import java.util.PriorityQueue;

class Solution {
    class Worker {
        int quality, wage;
        double ratio;
        public Worker(int q, int w) {
            quality = q;
            wage = w;
            ratio = (double) w / q;
        }
    }
    
    public double mincostToHireWorkers(int[] quality, int[] wage, int k) {
        int n = quality.length;
        Worker[] workers = new Worker[n];
        for(int i=0; i<n; i++) workers[i] = new Worker(quality[i], wage[i]);
        
        Arrays.sort(workers, (a, b) -> Double.compare(a.ratio, b.ratio));
        
        PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
        int sumQuality = 0;
        double minCost = Double.MAX_VALUE;
        
        for (Worker worker : workers) {
            pq.offer(worker.quality);
            sumQuality += worker.quality;
            
            if (pq.size() > k) {
                sumQuality -= pq.poll();
            }
            
            if (pq.size() == k) {
                minCost = Math.min(minCost, sumQuality * worker.ratio);
            }
        }
        
        return minCost;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N \log N)$ cho sort và PQ.
- Space: $\mathcal{O}(N)$ cho Array lưu Object và Max-Heap lưu Quality.

---

## 8. Advantage Shuffle (LeetCode 870)
**Đề bài:** Cho mảng `A` và `B`. Tìm hoán vị của `A` sao cho số lượng phần tử `A[i] > B[i]` là lớn nhất có thể.

**Phân tích thuật toán:**
- Chiến lược như đua ngựa của Tôn Tẫn: Sắp xếp cả `A` và `B`. Để so sánh `B` mà không mất vị trí ban đầu, ta dùng cấu trúc dữ liệu lưu index.
- Nếu phần tử lớn nhất hiện tại của `A` có thể thắng phần tử lớn nhất hiện tại của `B`, ta ghép chúng.
- Nếu không thể thắng, dùng phần tử lớn nhất của `A` này đi đấu với... phần tử này của `B` là vô ích, lấy phần tử tồi nhất (nhỏ nhất) của `A` ra để "thí mạng" (chấp nhận thua).
- Có thể dùng `TreeMap` (hoặc Two Pointers sau khi sort).

**Mã nguồn Java (Dùng Two Pointers):**
```java
import java.util.Arrays;
import java.util.PriorityQueue;

class Solution {
    public int[] advantageCount(int[] nums1, int[] nums2) {
        Arrays.sort(nums1);
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> b[1] - a[1]);
        
        for (int i = 0; i < nums2.length; i++) {
            pq.offer(new int[]{i, nums2[i]});
        }
        
        int[] res = new int[nums1.length];
        int left = 0, right = nums1.length - 1;
        
        while (!pq.isEmpty()) {
            int[] current = pq.poll();
            int index = current[0];
            int value = current[1];
            
            if (nums1[right] > value) {
                res[index] = nums1[right];
                right--;
            } else {
                res[index] = nums1[left];
                left++;
            }
        }
        
        return res;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N \log N)$ cho sort và đẩy vào PriorityQueue.
- Space: $\mathcal{O}(N)$ lưu PQ và mảng kết quả.

---

## 9. Minimum Deletion Cost to Avoid Repeating Letters (LeetCode 1578)
**Đề bài:** Cho chuỗi `s` và mảng `cost`. Bạn có thể xóa kí tự và tốn chi phí tương ứng. Cần làm cho không có 2 ký tự liên tiếp giống nhau. Tối thiểu hóa chi phí.

**Phân tích thuật toán:**
- Khi có 1 chuỗi các kí tự giống nhau liên tiếp (ví dụ 'aaaa'), chúng ta bắt buộc phải giữ lại duy nhất 1 kí tự.
- Để chi phí xóa bỏ là nhỏ nhất, ta nên giữ lại kí tự có `cost` cao nhất, và xóa đi các kí tự còn lại.
- Dùng Greedy duyệt qua các đoạn giống nhau liên tiếp, cộng dồn tổng `cost` và tìm `maxCost`. Chi phí phải trả cho nhóm đó là `sum - max`.

**Mã nguồn Java:**
```java
class Solution {
    public int minCost(String colors, int[] neededTime) {
        int minTotalCost = 0;
        int n = colors.length();
        
        int i = 0;
        while (i < n) {
            char currentChar = colors.charAt(i);
            int maxTime = neededTime[i];
            int sumTime = neededTime[i];
            int j = i + 1;
            
            while (j < n && colors.charAt(j) == currentChar) {
                maxTime = Math.max(maxTime, neededTime[j]);
                sumTime += neededTime[j];
                j++;
            }
            
            // Số lượng phần tử bị xóa trong đoạn này
            // cost = Tổng thời gian - thời gian lớn nhất cần giữ lại
            minTotalCost += (sumTime - maxTime);
            i = j;
        }
        
        return minTotalCost;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N)$ mỗi phần tử được xét và lướt qua đúng 1 lần.
- Space: $\mathcal{O}(1)$ thao tác trực tiếp trên biến đếm.

---

## 10. Two City Scheduling (LeetCode 1029)
**Đề bài:** Có $2N$ người, `costs[i] = [aCosti, bCosti]` là chi phí người `i` bay đến thành phố A và B. Yêu cầu tính tổng chi phí nhỏ nhất để đưa đúng $N$ người đến A và $N$ người đến B.

**Phân tích thuật toán:**
- Tư duy tham lam thông qua độ chênh lệch (Difference/Opportunity cost).
- Chênh lệch `costA - costB` cho biết người này nếu đi thành phố A thay vì B sẽ đắt hơn (hoặc rẻ hơn) bao nhiêu.
- Giá trị này càng âm (càng nhỏ), thì người này CÀNG PHẢI ĐI thành phố A. (Ví dụ `costA = 10, costB = 1000` $\rightarrow$ Diff = `-990` $\rightarrow$ cực kì ưu tiên A).
- Sort mảng `costs` dựa trên `costA - costB`. Nửa đầu cho đi A, nửa sau cho đi B.

**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int twoCitySchedCost(int[][] costs) {
        // Sắp xếp theo chênh lệch (costA - costB)
        Arrays.sort(costs, (a, b) -> (a[0] - a[1]) - (b[0] - b[1]));
        
        int totalCost = 0;
        int n = costs.length / 2;
        
        // N người đầu đi thành phố A
        for (int i = 0; i < n; i++) {
            totalCost += costs[i][0];
        }
        
        // N người sau đi thành phố B
        for (int i = n; i < costs.length; i++) {
            totalCost += costs[i][1];
        }
        
        return totalCost;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N \log N)$ cho việc sort mảng hai chiều.
- Space: $\mathcal{O}(\log N)$ không gian sort tùy thuộc implementation.

---

## 11. Minimum Number of Arrows to Burst Balloons (LeetCode 452)
**Đề bài chi tiết:**
Trên một mặt phẳng 2D, có một số quả bóng bay hình cầu được biểu diễn bằng một mảng 2D `points`, trong đó `points[i] = [x_start, x_end]` biểu thị tọa độ theo trục X của quả bóng thứ `i`.
Bạn có thể bắn các mũi tên thẳng lên trên từ trục X. Một quả bóng có `x_start <= x <= x_end` sẽ bị nổ nếu một mũi tên được bắn tại tọa độ `x`. Không có giới hạn về số lượng mũi tên.
Tìm số lượng mũi tên tối thiểu cần bắn để làm nổ tất cả các quả bóng.

**Phân tích thuật toán:**
- Sử dụng thuật toán Tham lam (Greedy).
- Sắp xếp các quả bóng theo điểm kết thúc (`x_end`) tăng dần. Việc sắp xếp theo điểm kết thúc giúp chúng ta luôn bắn mũi tên ở giới hạn xa nhất có thể của quả bóng hiện tại, từ đó cơ hội đâm xuyên qua các quả bóng tiếp theo là cao nhất.
- Khởi tạo mũi tên đầu tiên bắn ở điểm `x_end` của quả bóng đầu tiên.
- Duyệt qua các quả bóng còn lại, nếu quả bóng có `x_start` lớn hơn tọa độ mũi tên hiện tại, mũi tên không thể làm nổ quả bóng đó. Ta bắt buộc phải dùng thêm một mũi tên mới và cập nhật tọa độ mũi tên mới này bằng `x_end` của quả bóng vừa gặp.

**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int findMinArrowShots(int[][] points) {
        if (points == null || points.length == 0) return 0;
        
        // Sắp xếp theo điểm kết thúc, tránh tràn số khi so sánh
        Arrays.sort(points, (a, b) -> Integer.compare(a[1], b[1]));
        
        int arrows = 1;
        int currentEnd = points[0][1];
        
        for (int i = 1; i < points.length; i++) {
            // Nếu quả bóng hiện tại bắt đầu sau mũi tên hiện tại
            if (points[i][0] > currentEnd) {
                arrows++;
                currentEnd = points[i][1];
            }
        }
        
        return arrows;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N \log N)$ cho việc sắp xếp mảng.
- Space: $\mathcal{O}(\log N)$ hoặc $\mathcal{O}(N)$ không gian sắp xếp tùy thuộc ngôn ngữ.

---

## 12. Non-overlapping Intervals (LeetCode 435)
**Đề bài chi tiết:**
Cho một mảng các khoảng thời gian `intervals`, trong đó `intervals[i] = [start_i, end_i]`. Tìm số lượng khoảng thời gian tối thiểu cần loại bỏ để các khoảng thời gian còn lại không bị chồng chéo nhau.

**Phân tích thuật toán:**
- Tư duy tương tự bài "Burst Balloons". Vấn đề là chọn số lượng khoảng thời gian lớn nhất không bị chồng chéo.
- Sắp xếp mảng theo thời gian kết thúc (`end_i`) tăng dần. Việc ưu tiên khoảng thời gian kết thúc sớm nhất sẽ để lại nhiều không gian (thời gian) nhất cho các khoảng phía sau.
- Duyệt qua danh sách, lưu lại điểm kết thúc hiện tại (`currentEnd`).
- Nếu gặp một khoảng có thời gian bắt đầu lớn hơn hoặc bằng `currentEnd`, ta có thể giữ nó lại và cập nhật `currentEnd`.
- Nếu không, ta phải loại bỏ khoảng đó (tăng biến đếm).

**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        if (intervals == null || intervals.length == 0) return 0;
        
        // Sắp xếp theo end_i
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[1], b[1]));
        
        int removeCount = 0;
        int currentEnd = intervals[0][1];
        
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] < currentEnd) {
                // Chồng chéo, loại bỏ khoảng hiện tại
                removeCount++;
            } else {
                // Không chồng chéo, cập nhật currentEnd
                currentEnd = intervals[i][1];
            }
        }
        
        return removeCount;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N \log N)$ do thao tác sắp xếp.
- Space: $\mathcal{O}(\log N)$ không gian sắp xếp.

---

## 13. Jump Game II (LeetCode 45)
**Đề bài chi tiết:**
Cho một mảng số nguyên không âm `nums` độ dài $N$. Bạn ban đầu ở vị trí `nums[0]`. Mỗi phần tử `nums[i]` đại diện cho độ dài bước nhảy tối đa của bạn tại vị trí đó.
Mục tiêu là đạt đến vị trí cuối cùng `nums[n - 1]` với số bước nhảy tối thiểu.
(Giả định bạn luôn có thể đến đích).

**Phân tích thuật toán:**
- Sử dụng Greedy (có thể hiểu như BFS ẩn).
- Duy trì 2 ranh giới: `currentEnd` (ranh giới xa nhất của lần nhảy hiện tại) và `farthest` (điểm xa nhất có thể đạt được trong tương lai).
- Lặp qua mảng (không bao gồm phần tử cuối). Tại mỗi điểm, cập nhật `farthest = Math.max(farthest, i + nums[i])`.
- Khi đến `i == currentEnd` (nghĩa là đã thăm hết các điểm trong phạm vi của bước nhảy hiện tại), ta bắt buộc phải nhảy thêm 1 bước. Ta cộng `jumps++` và cập nhật `currentEnd = farthest`.

**Mã nguồn Java:**
```java
class Solution {
    public int jump(int[] nums) {
        int jumps = 0;
        int currentEnd = 0;
        int farthest = 0;
        
        // Không cần duyệt phần tử cuối vì khi đến đó là xong
        for (int i = 0; i < nums.length - 1; i++) {
            farthest = Math.max(farthest, i + nums[i]);
            
            // Nếu đã đi đến giới hạn của bước nhảy hiện tại
            if (i == currentEnd) {
                jumps++;
                currentEnd = farthest;
            }
        }
        
        return jumps;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N)$ duyệt mảng 1 lần duy nhất.
- Space: $\mathcal{O}(1)$ sử dụng biến hằng số.

---

## 14. Candy (LeetCode 135)
**Đề bài chi tiết:**
Có $N$ đứa trẻ đứng thành một hàng. Mỗi đứa trẻ được gán một điểm đánh giá (rating) được lưu trong mảng nguyên `ratings`.
Bạn đang phát kẹo cho những đứa trẻ này và phải tuân theo các yêu cầu:
1. Mỗi đứa trẻ phải có ít nhất 1 viên kẹo.
2. Những đứa trẻ có rating cao hơn người bên cạnh (trái hoặc phải) sẽ được nhiều kẹo hơn người đó.
Tìm số kẹo tối thiểu bạn cần phát.

**Phân tích thuật toán:**
- Đây là dạng toán Greedy theo 2 chiều (trái-phải và phải-trái).
- Khởi tạo mảng `candies` độ dài $N$ toàn số 1.
- Quét từ trái sang phải: Nếu `ratings[i] > ratings[i-1]`, ta đảm bảo đứa trẻ `i` có nhiều kẹo hơn đứa `i-1` bằng cách gán `candies[i] = candies[i-1] + 1`.
- Quét từ phải sang trái: Nếu `ratings[i] > ratings[i+1]`, ta đảm bảo đứa trẻ `i` có nhiều kẹo hơn đứa `i+1`. Nhưng do trước đó nó có thể đã được cấp nhiều kẹo từ lượt quét trái, ta gán `candies[i] = Math.max(candies[i], candies[i+1] + 1)`.
- Kết quả là tổng của mảng `candies`.

**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int candy(int[] ratings) {
        int n = ratings.length;
        int[] candies = new int[n];
        Arrays.fill(candies, 1);
        
        // Duyệt từ trái sang phải
        for (int i = 1; i < n; i++) {
            if (ratings[i] > ratings[i - 1]) {
                candies[i] = candies[i - 1] + 1;
            }
        }
        
        // Duyệt từ phải sang trái
        int sum = candies[n - 1];
        for (int i = n - 2; i >= 0; i--) {
            if (ratings[i] > ratings[i + 1]) {
                candies[i] = Math.max(candies[i], candies[i + 1] + 1);
            }
            sum += candies[i];
        }
        
        return sum;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N)$ gồm 2 vòng lặp qua mảng.
- Space: $\mathcal{O}(N)$ mảng lưu số lượng kẹo.

---

## 15. Reorganize String (LeetCode 767)
**Đề bài chi tiết:**
Cho một chuỗi `s`, sắp xếp lại các ký tự của chuỗi sao cho không có hai ký tự liền kề nào giống nhau.
Trả về chuỗi kết quả hợp lệ bất kỳ, hoặc chuỗi rỗng `""` nếu không thể thỏa mãn yêu cầu.

**Phân tích thuật toán:**
- Nếu một ký tự xuất hiện nhiều hơn `(N + 1) / 2` lần, thì không thể xếp được, sẽ luôn có 2 ký tự trùng nhau đứng cạnh nhau.
- Sử dụng Max-Heap lưu trữ ký tự theo tần suất. Tại mỗi bước, ta luôn ưu tiên nối ký tự có tần suất cao nhất vào chuỗi. Để tránh nối 2 ký tự giống nhau liên tiếp, mỗi lần ta lấy 2 ký tự xuất hiện nhiều nhất (pop 2 lần từ Heap), nối vào kết quả, giảm tần suất và đẩy lại vào Heap nếu tần suất $> 0$.

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;

class Solution {
    public String reorganizeString(String s) {
        int[] counts = new int[26];
        for (char c : s.toCharArray()) {
            counts[c - 'a']++;
        }
        
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> b[1] - a[1]);
        for (int i = 0; i < 26; i++) {
            if (counts[i] > 0) {
                // Không thể xếp được
                if (counts[i] > (s.length() + 1) / 2) return "";
                pq.offer(new int[]{i, counts[i]});
            }
        }
        
        StringBuilder sb = new StringBuilder();
        while (pq.size() >= 2) {
            int[] first = pq.poll();
            int[] second = pq.poll();
            
            sb.append((char) (first[0] + 'a'));
            sb.append((char) (second[0] + 'a'));
            
            if (--first[1] > 0) pq.offer(first);
            if (--second[1] > 0) pq.offer(second);
        }
        
        if (!pq.isEmpty()) {
            sb.append((char) (pq.poll()[0] + 'a'));
        }
        
        return sb.toString();
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N \log K)$ với $K=26$ là số lượng ký tự khác nhau, $N$ là độ dài chuỗi. Thêm và xóa vào Heap chỉ mất $\mathcal{O}(\log 26)$.
- Space: $\mathcal{O}(K)$ để lưu PriorityQueue và mảng tần suất.

---

## 16. Split Array into Consecutive Subsequences (LeetCode 659)
**Đề bài chi tiết:**
Cho mảng số nguyên `nums` được sắp xếp theo thứ tự không giảm. Hãy kiểm tra xem bạn có thể chia `nums` thành một hoặc nhiều chuỗi con rời rạc (subsequence), sao cho mỗi chuỗi con bao gồm các số nguyên liên tiếp nhau và có độ dài ít nhất là 3 hay không.

**Phân tích thuật toán:**
- Dùng Greedy kết hợp với 2 HashMap.
- `freqMap`: Đếm tần suất xuất hiện của các số chưa được xử lý.
- `appendMap`: Đếm số lượng các chuỗi con liên tiếp hiện tại đang chờ một số `x` nào đó để nối tiếp (ví dụ có chuỗi `{1,2,3}`, nó cần số `4`, nên ta lưu `appendMap.put(4, count + 1)`).
- Duyệt qua từng số `x` trong mảng:
    - Nếu `freqMap` của `x` bằng 0, tức là số này đã được dùng ở đâu đó, bỏ qua.
    - Ưu tiên 1: Gắn số `x` vào một chuỗi đã có trước đó. Nếu `appendMap` có mong chờ `x` (`appendMap.get(x) > 0`), ta dùng số này nối vào chuỗi cũ. Sau khi nối, chuỗi cũ giờ mong chờ số `x + 1` (giảm số lượng mong chờ `x`, tăng mong chờ `x + 1`).
    - Ưu tiên 2: Tạo chuỗi mới, nếu có đủ các số tiếp theo: `x+1` và `x+2` trong `freqMap`. Nếu có, tạo chuỗi mới và đánh dấu chuỗi này đang mong chờ số `x+3`.
    - Nếu không thỏa 1 và 2, trả về `false`.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public boolean isPossible(int[] nums) {
        Map<Integer, Integer> freqMap = new HashMap<>();
        Map<Integer, Integer> appendMap = new HashMap<>();
        
        for (int num : nums) {
            freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);
        }
        
        for (int num : nums) {
            if (freqMap.get(num) == 0) continue;
            
            if (appendMap.getOrDefault(num, 0) > 0) {
                // Ghép vào đuôi chuỗi hiện có
                appendMap.put(num, appendMap.get(num) - 1);
                appendMap.put(num + 1, appendMap.getOrDefault(num + 1, 0) + 1);
            } else if (freqMap.getOrDefault(num + 1, 0) > 0 && freqMap.getOrDefault(num + 2, 0) > 0) {
                // Bắt đầu chuỗi mới độ dài 3
                freqMap.put(num + 1, freqMap.get(num + 1) - 1);
                freqMap.put(num + 2, freqMap.get(num + 2) - 1);
                appendMap.put(num + 3, appendMap.getOrDefault(num + 3, 0) + 1);
            } else {
                return false;
            }
            
            freqMap.put(num, freqMap.get(num) - 1);
        }
        
        return true;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N)$ vì duyệt qua mảng vài lần và sử dụng HashMap (chi phí trung bình $\mathcal{O}(1)$).
- Space: $\mathcal{O}(N)$ để lưu hai HashMaps.

---

## 17. Construct Target Array With Multiple Sums (LeetCode 1354)
**Đề bài chi tiết:**
Cho mảng số nguyên `target`. Bắt đầu bằng một mảng có cùng kích thước gồm toàn số 1. Trong một bước, bạn có thể tính tổng tất cả các phần tử trong mảng hiện tại và thay thế một phần tử bất kỳ bằng tổng đó.
Kiểm tra xem có thể tạo được mảng `target` hay không.

**Phân tích thuật toán:**
- Tư duy đi ngược từ `target` về mảng gốc toàn số 1.
- Mọi bước thay thế đều làm cho phần tử được thay thế trở thành số lớn nhất. Do đó, phần tử lớn nhất hiện tại trong mảng chính là phần tử vừa được thay thế.
- Đặt `maxVal` là phần tử lớn nhất, `totalSum` là tổng các phần tử.
- Số ban đầu (trước khi thay) = `maxVal - (totalSum - maxVal)`. Thay vì chỉ trừ đi một lần (gây quá thời gian - TLE nếu số cực lớn), ta dùng phép chia lấy dư: `maxVal % (totalSum - maxVal)`.
- Dùng Max-Heap (PriorityQueue) để luôn tìm được `maxVal` một cách hiệu quả.
- Lưu ý các góc (edge cases): nếu tổng còn lại là 1, luôn đúng (vì 1 có thể thêm vào bất kì lần nào). Nếu phép Mod bằng 0 hoặc Mod == maxVal thì sai.

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;

class Solution {
    public boolean isPossible(int[] target) {
        if (target.length == 1) return target[0] == 1;
        
        PriorityQueue<Long> pq = new PriorityQueue<>((a, b) -> Long.compare(b, a));
        long totalSum = 0;
        
        for (int num : target) {
            pq.offer((long) num);
            totalSum += num;
        }
        
        while (pq.peek() > 1) {
            long max = pq.poll();
            long restSum = totalSum - max;
            
            // Nếu tổng phần còn lại = 1, luôn đưa max về 1 được bằng cách trừ đi n-1 lần restSum
            if (restSum == 1) return true;
            
            // restSum phải < max và > 0 thì mới lùi lại được
            if (restSum == 0 || max <= restSum) return false;
            
            long prev = max % restSum;
            
            // Không thể có giá trị 0
            if (prev == 0) return false;
            
            pq.offer(prev);
            totalSum = restSum + prev;
        }
        
        return true;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N \log N \log (\max\_target))$. Mỗi phần tử bị giảm giá trị theo logarit nhờ toán tử modulo, chi phí Heap là $\mathcal{O}(\log N)$.
- Space: $\mathcal{O}(N)$ cho Priority Queue.

---

## 18. Reduce Array Size to The Half (LeetCode 1338)
**Đề bài chi tiết:**
Cho một mảng `arr`. Bạn có thể chọn một tập hợp các số nguyên bất kỳ, loại bỏ tất cả các lần xuất hiện của các số đó trong mảng.
Trả về kích thước tối thiểu của tập hợp sao cho ít nhất một nửa số nguyên của mảng bị loại bỏ.

**Phân tích thuật toán:**
- Yêu cầu xóa ít số loại phần tử nhất nhưng lại xóa đi được $\ge N / 2$ phần tử mảng. Do đó, Greedy là: Ưu tiên xóa phần tử có tần suất xuất hiện cao nhất.
- Đếm tần suất xuất hiện của từng số (HashMap).
- Sắp xếp các tần suất này theo thứ tự giảm dần.
- Bắt đầu lấy các tần suất cộng dồn. Tăng biến đếm tập hợp. Khi nào tổng tần suất $\ge N / 2$ thì dừng và trả về biến đếm.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;
import java.util.PriorityQueue;

class Solution {
    public int minSetSize(int[] arr) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int num : arr) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }
        
        // PriorityQueue chứa các count (tần suất) lớn nhất trước
        PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
        for (int count : map.values()) {
            pq.offer(count);
        }
        
        int removedCount = 0;
        int setSize = 0;
        int half = arr.length / 2;
        
        while (removedCount < half && !pq.isEmpty()) {
            removedCount += pq.poll();
            setSize++;
        }
        
        return setSize;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N \log N)$ có thể dùng Sorting hoặc PriorityQueue để lấy tần suất.
- Space: $\mathcal{O}(N)$ lưu Map và Queue.

---

## 19. Gas Station (LeetCode 134)
**Đề bài chi tiết:**
Có $N$ trạm xăng xếp thành vòng tròn, lượng xăng ở trạm `i` là `gas[i]`. Bạn có một chiếc xe với bình xăng vô hạn, đi từ trạm `i` đến `i+1` tốn `cost[i]` lít xăng. Bạn có thể bắt đầu từ một trạm bất kỳ với bình xăng rỗng.
Tìm vị trí xuất phát để có thể đi đúng một vòng trở về vị trí đó. Trả về vị trí xuất phát, hoặc `-1` nếu không thể. Dữ liệu đảm bảo nghiệm (nếu có) là duy nhất.

**Phân tích thuật toán:**
- Đầu tiên, kiểm tra tổng lượng xăng và tổng chi phí đi đường. Nếu tổng lượng xăng $<$ tổng chi phí, không thể đi hết vòng $\rightarrow$ Trả về `-1`.
- Nếu có nghiệm, ta dùng Greedy. Bắt đầu từ trạm 0, tích lũy lượng xăng `currentTank += gas[i] - cost[i]`.
- Nếu `currentTank < 0`, chứng tỏ bạn không thể đi tới trạm tiếp theo từ một trong các trạm xuất phát trước đó. Do mọi trạm từ `start` đến `i` đều dẫn đến sự cố hết xăng tại `i`, trạm tiếp theo có tiềm năng làm điểm xuất phát phải là `i + 1`. Ta gán `startStation = i + 1` và reset `currentTank = 0`.

**Mã nguồn Java:**
```java
class Solution {
    public int canCompleteCircuit(int[] gas, int[] cost) {
        int totalTank = 0;
        int currentTank = 0;
        int startStation = 0;
        
        for (int i = 0; i < gas.length; i++) {
            int net = gas[i] - cost[i];
            totalTank += net;
            currentTank += net;
            
            // Hết xăng, chọn trạm tiếp theo làm điểm bắt đầu
            if (currentTank < 0) {
                startStation = i + 1;
                currentTank = 0;
            }
        }
        
        // Nếu tổng lượng xăng >= chi phí thì luôn có một giải pháp
        return totalTank >= 0 ? startStation : -1;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N)$ duy nhất một vòng lặp qua mảng.
- Space: $\mathcal{O}(1)$ sử dụng biến đếm.

---

## 20. Remove K Digits (LeetCode 402)
**Đề bài chi tiết:**
Cho số nguyên không âm `num` được biểu diễn dưới dạng chuỗi và một số nguyên `k`. Xóa `k` chữ số khỏi `num` sao cho số nguyên mới tạo thành là nhỏ nhất có thể.

**Phân tích thuật toán:**
- Tư duy Greedy: Khi so sánh hai số cùng độ dài, số có chữ số nhỏ hơn ở trọng số cao hơn (vị trí đầu) sẽ nhỏ hơn (Ví dụ `12...` luôn nhỏ hơn `21...`).
- Nếu duyệt từ trái qua phải, nếu thấy một chữ số hiện tại nhỏ hơn chữ số trước đó, ta nên xóa chữ số trước đó để chữ số hiện tại lên thế chỗ (Ví dụ có số `143`, vì `3 < 4`, xóa `4` thành `13` nhỏ hơn `14`).
- Kỹ thuật phù hợp nhất là **Monotonic Stack** (Stack đơn điệu tăng dần). Khi một số nhỏ hơn đỉnh của stack, ta `pop` đỉnh ra và giảm biến `k`.
- Xử lý các edge cases: 
    - Nếu vẫn còn biến `k` (ví dụ số đã tăng dần `1234`, chưa pop phần tử nào), thì xóa `k` ký tự cuối từ Stack.
    - Xóa các chữ số `0` đứng đầu kết quả.
    - Chuỗi kết quả có thể rỗng, khi đó trả về `"0"`.

**Mã nguồn Java:**
```java
import java.util.Deque;
import java.util.ArrayDeque;

class Solution {
    public String removeKdigits(String num, int k) {
        if (num.length() == k) return "0";
        
        Deque<Character> stack = new ArrayDeque<>();
        
        for (char c : num.toCharArray()) {
            // Khi đỉnh Stack lớn hơn phần tử hiện tại và vẫn còn k lượt xóa
            while (!stack.isEmpty() && k > 0 && stack.peekLast() > c) {
                stack.pollLast();
                k--;
            }
            stack.offerLast(c);
        }
        
        // Nếu mảng tăng dần, xóa tiếp ở cuối cho đủ k
        while (k > 0) {
            stack.pollLast();
            k--;
        }
        
        StringBuilder sb = new StringBuilder();
        boolean leadingZero = true;
        
        // Xây dựng chuỗi và loại bỏ số 0 vô nghĩa ở đầu
        for (char c : stack) {
            if (leadingZero && c == '0') continue;
            leadingZero = false;
            sb.append(c);
        }
        
        return sb.length() == 0 ? "0" : sb.toString();
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N)$ mỗi chữ số được đẩy vào và kéo ra khỏi Stack nhiều nhất 1 lần.
- Space: $\mathcal{O}(N)$ cho Deque/Stack và StringBuilder lưu kết quả.

---

## 21. Boats to Save People (LeetCode 881)
**Đề bài chi tiết:**
Bạn được cung cấp mảng `people`, trong đó `people[i]` là cân nặng của người thứ `i`, và một chiếc thuyền có thể chở trọng tải tối đa là `limit`.
Mỗi chiếc thuyền có thể chở nhiều nhất 2 người cùng một lúc, miễn là tổng cân nặng của họ không vượt quá `limit`.
Tìm số lượng thuyền tối thiểu cần thiết để chở tất cả mọi người.

**Phân tích thuật toán:**
- Dùng thuật toán Tham lam kết hợp Hai con trỏ (Two Pointers).
- Sắp xếp mảng `people` theo chiều tăng dần.
- Dùng con trỏ `left` chỉ vào người nhẹ nhất (đầu mảng) và `right` chỉ vào người nặng nhất (cuối mảng).
- Tại mỗi bước, ưu tiên xếp người nặng nhất (`people[right]`) lên thuyền. Sau đó, ta kiểm tra xem thuyền còn chỗ cho người nhẹ nhất hiện tại không (nghĩa là `people[left] + people[right] <= limit`).
- Nếu có, ta cho cả người nhẹ nhất lên thuyền (`left++`).
- Trong mọi trường hợp, người nặng nhất chắc chắn phải lên một chiếc thuyền (có thể đi cùng người nhẹ nhất hoặc đi một mình). Do đó, ta luôn giảm `right--` và tăng số thuyền `boats++`.

**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int numRescueBoats(int[] people, int limit) {
        Arrays.sort(people);
        
        int left = 0;
        int right = people.length - 1;
        int boats = 0;
        
        while (left <= right) {
            if (people[left] + people[right] <= limit) {
                // Thuyền chở được cả hai
                left++;
            }
            // Trong mọi trường hợp, người nặng nhất đều lên thuyền
            right--;
            boats++;
        }
        
        return boats;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N \log N)$ do yêu cầu sắp xếp mảng.
- Space: $\mathcal{O}(\log N)$ không gian sắp xếp.

---

## 22. Minimum Operations to Make Array Equal (LeetCode 1551)
**Đề bài chi tiết:**
Cho một mảng độ dài $N$ có các phần tử được định nghĩa bằng công thức: `arr[i] = (2 * i) + 1` với `0 <= i < n`.
Trong một thao tác, bạn có thể chọn hai chỉ số `x` và `y` ($x \neq y$), sau đó trừ đi 1 ở `arr[x]` và cộng thêm 1 vào `arr[y]`. Thao tác này đảm bảo tổng của mảng luôn không đổi.
Tìm số thao tác tối thiểu để làm cho tất cả các phần tử của mảng bằng nhau.

**Phân tích thuật toán:**
- Đây là bài toán mang tính chất Toán học.
- Mảng khởi tạo là một cấp số cộng: 1, 3, 5, 7, 9...
- Trung bình cộng của mảng này luôn là $N$ (bởi vì tổng mảng là $N^2$).
- Để làm tất cả các số bằng nhau, ta cần biến mọi số thành $N$. Số thao tác sẽ bằng tổng chênh lệch giữa phần tử đích $N$ và tất cả các số nhỏ hơn $N$ (hoặc lớn hơn $N$, kết quả giống nhau do tính đối xứng).
- Nếu $N$ chẵn: Các số nhỏ hơn $N$ là 1, 3, 5... có $N/2$ số. Tổng khoảng cách là $(N/2)^2$.
- Nếu $N$ lẻ: Các số nhỏ hơn $N$ là 1, 3, 5... có $N/2$ (làm tròn xuống) số. Tổng khoảng cách là $(N/2) \cdot (N/2 + 1)$.
- Có thể thu gọn lại bằng công thức chung: `(n * n) / 4`.

**Mã nguồn Java:**
```java
class Solution {
    public int minOperations(int n) {
        return (n * n) / 4;
        
        /* 
        // Hoặc tính theo vòng lặp (O(N)):
        int target = n;
        int operations = 0;
        for (int i = 0; i < n / 2; i++) {
            operations += (target - (2 * i + 1));
        }
        return operations;
        */
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(1)$ sử dụng công thức toán học.
- Space: $\mathcal{O}(1)$.

---

## 23. Car Pooling (LeetCode 1094)
**Đề bài chi tiết:**
Có một chiếc xe có sức chứa là `capacity`. Mảng `trips` trong đó `trips[i] = [num_passengers, start_location, end_location]` mô tả chuyến đi thứ `i` đón `num_passengers` người tại `start_location` và trả họ tại `end_location`.
Hành trình luôn đi về một hướng từ tây sang đông. Trả về `true` nếu bạn có thể chở tất cả hành khách, ngược lại trả về `false`.

**Phân tích thuật toán:**
- Dùng kỹ thuật Sweeping Line (Đường quét).
- Tạo mảng `timestamp` kích thước 1001 (do $0 \le location \le 1000$).
- Duyệt qua từng chuyến đi, cộng số khách lên tại điểm đón: `timestamp[start] += num_passengers`, và trừ số khách xuống tại điểm trả: `timestamp[end] -= num_passengers`.
- Sau đó quét mảng `timestamp` từ 0 đến 1000, cộng dồn số lượng khách hiện có trên xe. Tại bất kỳ thời điểm nào nếu tổng số khách vượt quá `capacity`, trả về `false`.

**Mã nguồn Java:**
```java
class Solution {
    public boolean carPooling(int[][] trips, int capacity) {
        int[] locations = new int[1001];
        
        for (int[] trip : trips) {
            locations[trip[1]] += trip[0]; // Khách lên
            locations[trip[2]] -= trip[0]; // Khách xuống
        }
        
        int currentPassengers = 0;
        for (int i = 0; i < 1001; i++) {
            currentPassengers += locations[i];
            if (currentPassengers > capacity) {
                return false;
            }
        }
        
        return true;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N + M)$ với $N$ là số chuyến đi (trips length), $M$ là 1001.
- Space: $\mathcal{O}(M)$ với $M=1001$ là kích thước mảng hằng số.

---

## 24. Maximum Area of a Piece of Cake After Horizontal and Vertical Cuts (LeetCode 1465)
**Đề bài chi tiết:**
Bạn có một chiếc bánh hình chữ nhật có kích thước `h x w` (chiều cao x chiều rộng) và hai mảng số nguyên `horizontalCuts` và `verticalCuts` biểu thị vị trí cắt ngang và cắt dọc dọc theo chiếc bánh.
Trả về diện tích lớn nhất của miếng bánh sau khi cắt. Do đáp án có thể lớn nên cần trả về kết quả modulo $10^9 + 7$.

**Phân tích thuật toán:**
- Để có miếng bánh diện tích lớn nhất, ta cần tìm được khoảng cách lớn nhất giữa hai đường cắt ngang kề nhau (chiều cao max) và khoảng cách lớn nhất giữa hai đường cắt dọc kề nhau (chiều rộng max).
- Sắp xếp mảng `horizontalCuts` và tính các khoảng cách giữa `0`, các điểm cắt, và `h`.
- Sắp xếp mảng `verticalCuts` và tính các khoảng cách giữa `0`, các điểm cắt, và `w`.
- Lấy `maxHeight * maxWidth` và modulo cho $10^9 + 7$. Cẩn thận tràn số nên dùng kiểu `long` trước khi nhân.

**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int maxArea(int h, int w, int[] horizontalCuts, int[] verticalCuts) {
        Arrays.sort(horizontalCuts);
        Arrays.sort(verticalCuts);
        
        long maxH = Math.max(horizontalCuts[0], h - horizontalCuts[horizontalCuts.length - 1]);
        for (int i = 1; i < horizontalCuts.length; i++) {
            maxH = Math.max(maxH, horizontalCuts[i] - horizontalCuts[i - 1]);
        }
        
        long maxW = Math.max(verticalCuts[0], w - verticalCuts[verticalCuts.length - 1]);
        for (int i = 1; i < verticalCuts.length; i++) {
            maxW = Math.max(maxW, verticalCuts[i] - verticalCuts[i - 1]);
        }
        
        int mod = 1_000_000_007;
        return (int) ((maxH * maxW) % mod);
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(H \log H + V \log V)$ do sắp xếp mảng (H, V là số đường cắt).
- Space: $\mathcal{O}(1)$ đến $\mathcal{O}(\log H + \log V)$ bộ nhớ bổ sung cho sắp xếp.

---

## 25. Broken Calculator (LeetCode 991)
**Đề bài chi tiết:**
Có một máy tính bị hỏng. Ban đầu nó hiển thị số `startValue`.
Máy tính chỉ có 2 nút:
1. Nhân đôi số hiện tại.
2. Trừ số hiện tại đi 1.
Tính số thao tác ít nhất cần thiết để biến `startValue` thành `target`.

**Phân tích thuật toán:**
- Tư duy đi ngược từ `target` về `startValue`. Tại `target`, ta có 2 nút ngược lại:
    1. Chia đôi (nếu chẵn).
    2. Cộng 1 (nếu lẻ).
- Lý do đi ngược là tối ưu: Phép nhân đôi ở chiều thuận (hoặc chia đôi ở chiều ngược) sẽ đưa hai số tiến lại gần nhau nhanh nhất. Do đó ta tham lam chia đôi `target` bất cứ khi nào có thể.
- Nếu `target > startValue`: 
    - Nếu `target` chẵn, ta bắt buộc phải chia 2.
    - Nếu `target` lẻ, ta bắt buộc phải cộng 1 (để trở thành số chẵn và có thể chia 2 tiếp).
- Nếu `target <= startValue`: Máy tính không thể chia nữa, chỉ có thể dùng phép ngược của trừ là "Cộng 1", tốn đúng `startValue - target` bước.

**Mã nguồn Java:**
```java
class Solution {
    public int brokenCalc(int startValue, int target) {
        int operations = 0;
        
        while (target > startValue) {
            operations++;
            if (target % 2 == 1) {
                target++;
            } else {
                target /= 2;
            }
        }
        
        // Nếu target < startValue, tốn thêm số bước bằng sự chênh lệch
        return operations + (startValue - target);
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(\log(\text{target}/\text{startValue}))$ mỗi lần lặp, `target` thu nhỏ khoảng phân nửa.
- Space: $\mathcal{O}(1)$.

---

## 26. Bag of Tokens (LeetCode 948)
**Đề bài chi tiết:**
Bạn có một số điểm `power` ban đầu và số điểm `score = 0`. Có một mảng `tokens` nơi `tokens[i]` là sức mạnh của token `i`.
Bạn có thể đánh các token theo thứ tự bất kỳ, mỗi token chỉ được dùng 1 lần, theo một trong 2 cách:
1. Lật ngửa (Face-up): Nếu `power >= tokens[i]`, bạn mất đi `tokens[i]` power và nhận được 1 score.
2. Lật úp (Face-down): Nếu `score >= 1`, bạn mất đi 1 score và nhận lại được `tokens[i]` power.
Trả về `score` lớn nhất có thể đạt được.

**Phân tích thuật toán:**
- Dùng Greedy và Two Pointers.
- Để tối đa hóa `score`, ta nên đổi power để lấy điểm ở những token có phí (power) nhỏ nhất.
- Khi cần có thêm power để chơi, ta hi sinh 1 điểm `score` để ăn token có power cao nhất.
- Cách làm: Sắp xếp `tokens` tăng dần. Dùng `left` ăn các token nhỏ, dùng `right` ăn các token lớn khi hết điểm power. Theo dõi điểm lớn nhất từng đạt được `maxScore`.

**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int bagOfTokensScore(int[] tokens, int power) {
        Arrays.sort(tokens);
        
        int left = 0, right = tokens.length - 1;
        int score = 0;
        int maxScore = 0;
        
        while (left <= right) {
            if (power >= tokens[left]) {
                // Có thể đổi power lấy score
                power -= tokens[left];
                score++;
                maxScore = Math.max(maxScore, score);
                left++;
            } else if (score > 0) {
                // Đổi score để lấy token to nhất
                power += tokens[right];
                score--;
                right--;
            } else {
                // Không có đủ power và không có đủ score để chơi tiếp
                break;
            }
        }
        
        return maxScore;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N \log N)$ cho thao tác sắp xếp.
- Space: $\mathcal{O}(\log N)$ không gian sắp xếp.

---

## 27. Least Number of Unique Integers after K Removals (LeetCode 1481)
**Đề bài chi tiết:**
Cho một mảng `arr` và một số nguyên `k`. Loại bỏ chính xác `k` phần tử để số lượng các số nguyên duy nhất còn lại trong mảng là ít nhất. Trả về số lượng số nguyên duy nhất ít nhất đó.

**Phân tích thuật toán:**
- Sử dụng Greedy: Để tối thiểu hóa số lượng số nguyên duy nhất, ta nên loại bỏ toàn bộ một nhóm các số giống nhau (xóa sạch chúng khỏi mảng). Để làm điều đó dễ nhất với `k` lượt, ta ưu tiên loại bỏ các số có tần suất xuất hiện nhỏ nhất.
- Dùng HashMap để đếm tần suất của mỗi phần tử.
- Lấy các tần suất đó ra một danh sách và sắp xếp tăng dần.
- Duyệt qua các tần suất, trừ dần đi `k`. Nếu `k` đủ lớn để trừ hết tần suất của một số, ta đã loại bỏ số đó hoàn toàn $\rightarrow$ Tổng số unique giảm đi 1.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;
import java.util.PriorityQueue;

class Solution {
    public int findLeastNumOfUniqueInts(int[] arr, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int num : arr) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }
        
        PriorityQueue<Integer> pq = new PriorityQueue<>(map.values());
        int uniqueCount = map.size();
        
        while (!pq.isEmpty() && k > 0) {
            int freq = pq.poll();
            if (k >= freq) {
                k -= freq;
                uniqueCount--;
            } else {
                break;
            }
        }
        
        return uniqueCount;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N \log M)$ với $N$ là kích thước mảng, $M$ là số lượng phần tử duy nhất do dùng PriorityQueue. (Có thể dùng mảng tần suất cho $\mathcal{O}(N)$).
- Space: $\mathcal{O}(M)$ cho HashMap và Queue.

---

## 28. Largest Number (LeetCode 179)
**Đề bài chi tiết:**
Cho một danh sách các số nguyên không âm `nums`, hãy sắp xếp chúng sao cho sau khi nối tất cả các số lại, chúng tạo thành số lớn nhất. Kết quả trả về dưới dạng chuỗi vì nó có thể rất lớn.

**Phân tích thuật toán:**
- Dùng Custom Comparator trong sắp xếp chuỗi.
- Chuyển mọi số thành String. So sánh 2 chuỗi `A` và `B` xem nên đặt cái nào trước.
- Không thể so sánh `A > B` bình thường (ví dụ `"3"` và `"30"`, `"30"` lớn hơn, nhưng ghép `"330"` > `"303"` nên `"3"` phải đứng trước).
- Logic chuẩn: Trật tự của $A$ và $B$ được quyết định bằng cách so sánh chuỗi ghép: $A+B$ với $B+A$. Nếu $A+B > B+A$, $A$ phải đứng trước $B$.
- Chú ý Edge Case: Nếu mảng toàn số `0` (ví dụ `[0,0]`), sẽ ghép thành `"00"`. Ta chỉ cần trả về `"0"` nếu ký tự đầu tiên của chuỗi đã sắp xếp là `'0'`.

**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public String largestNumber(int[] nums) {
        String[] strs = new String[nums.length];
        for (int i = 0; i < nums.length; i++) {
            strs[i] = String.valueOf(nums[i]);
        }
        
        // Sắp xếp theo logic A+B so với B+A
        Arrays.sort(strs, (a, b) -> {
            String order1 = a + b;
            String order2 = b + a;
            // Xếp giảm dần
            return order2.compareTo(order1);
        });
        
        // Nếu số lớn nhất là "0" thì trả về "0"
        if (strs[0].equals("0")) {
            return "0";
        }
        
        StringBuilder sb = new StringBuilder();
        for (String s : strs) {
            sb.append(s);
        }
        
        return sb.toString();
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N \log N \cdot k)$ trong đó $N$ là số lượng phần tử và $k$ là độ dài trung bình của chuỗi do thao tác so sánh nối chuỗi.
- Space: $\mathcal{O}(N \cdot k)$ để lưu mảng các chuỗi phụ.

---

## 29. Minimum Swaps to Make Strings Equal (LeetCode 1247)
**Đề bài chi tiết:**
Cho 2 chuỗi `s1` và `s2` có cùng độ dài chỉ bao gồm các ký tự 'x' và 'y'.
Nhiệm vụ của bạn là làm cho `s1` giống `s2`. Trong mỗi thao tác swap, chọn 1 ký tự ở `s1` và đổi cho 1 ký tự ở `s2` tại vị trí bất kỳ. Tìm số bước swap tối thiểu, hoặc `-1` nếu không thể.

**Phân tích thuật toán:**
- Ta chỉ quan tâm đến các vị trí mà `s1[i] != s2[i]`. Có hai loại cặp:
    - Loại 1: `s1[i] = 'x'`, `s2[i] = 'y'` (gọi là `xy`).
    - Loại 2: `s1[i] = 'y'`, `s2[i] = 'x'` (gọi là `yx`).
- Có 2 quy tắc swap:
    - Nếu ta có 2 cặp cùng loại (ví dụ 2 cặp `xy`), ta chỉ cần 1 thao tác swap chéo để làm 2 vị trí đó khớp nhau. Nên tốn $1$ bước.
    - Nếu ta có 1 cặp `xy` và 1 cặp `yx`, ta phải mất $2$ bước swap để khớp chúng (đổi `s1` lấy `s2` biến 1 cặp thành `yy` / `xx`, rồi lại đổi chéo lần nữa).
- Do đó:
    - Đếm tổng số `xy` và `yx`.
    - Nếu tổng `xy + yx` lẻ $\rightarrow$ không bao giờ giải được, trả về `-1`.
    - Lấy các cặp cùng loại ghép với nhau: `xy / 2` bước + `yx / 2` bước.
    - Phần dư (`xy % 2` và `yx % 2`) sẽ luôn bằng nhau. Nếu dư, tốn thêm 2 bước.

**Mã nguồn Java:**
```java
class Solution {
    public int minimumSwap(String s1, String s2) {
        int xy = 0;
        int yx = 0;
        
        for (int i = 0; i < s1.length(); i++) {
            char c1 = s1.charAt(i);
            char c2 = s2.charAt(i);
            
            if (c1 == 'x' && c2 == 'y') xy++;
            else if (c1 == 'y' && c2 == 'x') yx++;
        }
        
        // Nếu tổng số cặp lỗi là số lẻ, không thể đổi
        if ((xy + yx) % 2 != 0) return -1;
        
        // Mỗi 2 cặp giống nhau mất 1 bước,
        // Phần dư mất 2 bước
        return xy / 2 + yx / 2 + (xy % 2) * 2;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N)$ với vòng lặp quét qua chuỗi 1 lần.
- Space: $\mathcal{O}(1)$ sử dụng biến đếm.

---

## 30. Best Time to Buy and Sell Stock with Transaction Fee (LeetCode 714)
**Đề bài chi tiết:**
Cho mảng `prices` chứa giá của một cổ phiếu trong từng ngày, và số nguyên `fee` là phí của mỗi giao dịch. Bạn có thể thực hiện nhiều giao dịch, nhưng phải bán trước khi mua lại, và mỗi lần bán (hoặc mua) phải trả phí `fee` một lần.
Tìm lợi nhuận lớn nhất có thể.

**Phân tích thuật toán:**
- Dạng thuật toán Quy hoạch động với tối ưu không gian hoặc State Machine / Greedy.
- Duy trì hai trạng thái tại ngày thứ `i`:
    - `cash` (Lợi nhuận lớn nhất khi tay Không Cầm Cổ Phiếu ở cuối ngày).
    - `hold` (Lợi nhuận lớn nhất khi tay Đang Cầm Cổ Phiếu ở cuối ngày).
- Nếu không giữ cổ phiếu: ta có thể nghỉ (`cash` không đổi) hoặc bán cổ phiếu vừa giữ (`hold + prices[i] - fee`). $\rightarrow$ `cash = Math.max(cash, hold + prices[i] - fee)`
- Nếu giữ cổ phiếu: ta có thể nghỉ (`hold` không đổi) hoặc mua cổ phiếu mới bằng tiền mặt có sẵn (`cash - prices[i]`). $\rightarrow$ `hold = Math.max(hold, cash - prices[i])`
- Ban đầu: `cash = 0`, `hold = -prices[0]`.
- (Có thể thu gọn thuật toán thành một dạng Greedy theo xu hướng đỉnh-đáy).

**Mã nguồn Java:**
```java
class Solution {
    public int maxProfit(int[] prices, int fee) {
        int cash = 0; // Lợi nhuận nếu tay không
        int hold = -prices[0]; // Lợi nhuận nếu cầm cổ phiếu
        
        for (int i = 1; i < prices.length; i++) {
            // Update cash and hold simultaneously (hoặc tuần tự vẫn đúng do không ảnh hưởng vòng lặp)
            cash = Math.max(cash, hold + prices[i] - fee);
            hold = Math.max(hold, cash - prices[i]);
        }
        
        return cash;
    }
}
```
**Độ phức tạp:**
- Time: $\mathcal{O}(N)$ duyệt mảng giá trị 1 chiều 1 lần.
- Space: $\mathcal{O}(1)$ dùng 2 biến để tracking trạng thái.
