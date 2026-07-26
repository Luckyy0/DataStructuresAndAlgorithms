# Bài tập: Two Heaps Pattern

## 1. Find Median from Data Stream (LeetCode 295)
### Đề bài chi tiết
Thiết kế một cấu trúc dữ liệu hỗ trợ việc tính toán phần tử trung vị (median) từ một luồng dữ liệu (data stream).
- `addNum(int num)`: Thêm một số nguyên vào luồng dữ liệu.
- `findMedian()`: Trả về số trung vị của tất cả các phần tử cho đến thời điểm hiện tại.

### Phân tích thuật toán
Sử dụng mẫu Two Heaps.
- **Max-Heap** để chứa nửa nhỏ hơn của các số.
- **Min-Heap** để chứa nửa lớn hơn của các số.
- Cân bằng hai Heaps sao cho kích thước Max-Heap có thể lớn hơn Min-Heap nhiều nhất là 1.
- Nếu cả hai Heaps bằng nhau, trung vị là trung bình của 2 đỉnh. Nếu không, nó là đỉnh của Max-Heap.

### Mã nguồn Java
```java
class MedianFinder {
    private PriorityQueue<Integer> maxHeap;
    private PriorityQueue<Integer> minHeap;

    public MedianFinder() {
        maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        minHeap = new PriorityQueue<>();
    }

    public void addNum(int num) {
        if (maxHeap.isEmpty() || num <= maxHeap.peek()) {
            maxHeap.offer(num);
        } else {
            minHeap.offer(num);
        }

        // Cân bằng hai heaps
        if (maxHeap.size() > minHeap.size() + 1) {
            minHeap.offer(maxHeap.poll());
        } else if (minHeap.size() > maxHeap.size()) {
            maxHeap.offer(minHeap.poll());
        }
    }

    public double findMedian() {
        if (maxHeap.size() == minHeap.size()) {
            return (maxHeap.peek() / 2.0) + (minHeap.peek() / 2.0);
        }
        return maxHeap.peek();
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(log N)` cho `addNum`, `O(1)` cho `findMedian`.
- **Không gian (Space Complexity)**: `O(N)` để lưu dữ liệu.

## 2. Sliding Window Median (LeetCode 480)
### Đề bài chi tiết
Cho mảng số nguyên `nums` và số nguyên `k`. Một cửa sổ trượt kích thước `k` di chuyển từ trái sang phải. Trả về mảng chứa trung vị của mỗi cửa sổ trượt.

### Phân tích thuật toán
Sử dụng Two Heaps kết hợp với kỹ thuật **Lazy Deletion** hoặc sử dụng `TreeSet` trong Java. Vì PriorityQueue trong Java tốn `O(N)` để xóa một phần tử, ta có thể dùng `HashMap` để lưu số lượng phần tử cần xóa (khất lại việc xóa cho đến khi phần tử đó nằm ở đỉnh của Heap).

### Mã nguồn Java
```java
class Solution {
    public double[] medianSlidingWindow(int[] nums, int k) {
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        Map<Integer, Integer> invalidCount = new HashMap<>();
        
        double[] result = new double[nums.length - k + 1];
        
        for (int i = 0; i < k; i++) {
            maxHeap.offer(nums[i]);
        }
        for (int i = 0; i < k / 2; i++) {
            minHeap.offer(maxHeap.poll());
        }
        
        for (int i = k; i <= nums.length; i++) {
            // Lấy median
            if (k % 2 == 1) {
                result[i - k] = (double) maxHeap.peek();
            } else {
                result[i - k] = ((double) maxHeap.peek() / 2.0) + ((double) minHeap.peek() / 2.0);
            }
            
            if (i == nums.length) break;
            
            int outNum = nums[i - k];
            int inNum = nums[i];
            
            int balance = 0; // Thay đổi cân bằng giữa hai heaps
            if (outNum <= maxHeap.peek()) balance--;
            else balance++;
            
            invalidCount.put(outNum, invalidCount.getOrDefault(outNum, 0) + 1);
            
            if (!maxHeap.isEmpty() && inNum <= maxHeap.peek()) {
                balance++;
                maxHeap.offer(inNum);
            } else {
                balance--;
                minHeap.offer(inNum);
            }
            
            // Re-balance
            if (balance < 0) {
                maxHeap.offer(minHeap.poll());
                balance++;
            }
            if (balance > 0) {
                minHeap.offer(maxHeap.poll());
                balance--;
            }
            
            // Lazy deletion
            while (!maxHeap.isEmpty() && invalidCount.getOrDefault(maxHeap.peek(), 0) > 0) {
                invalidCount.put(maxHeap.peek(), invalidCount.get(maxHeap.peek()) - 1);
                maxHeap.poll();
            }
            while (!minHeap.isEmpty() && invalidCount.getOrDefault(minHeap.peek(), 0) > 0) {
                invalidCount.put(minHeap.peek(), invalidCount.get(minHeap.peek()) - 1);
                minHeap.poll();
            }
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: `O(N log K)`.
- **Không gian (Space)**: `O(K)`.

## 3. IPO (Maximize Capital) (LeetCode 502)
### Đề bài chi tiết
Bạn có `k` dự án có thể chọn để làm, với vốn ban đầu `W`. Mỗi dự án `i` yêu cầu vốn đầu tư `Capital[i]` và đem lại lợi nhuận `Profits[i]`. Mục tiêu là tối đa hóa tổng vốn sau khi hoàn thành nhiều nhất `k` dự án.

### Phân tích thuật toán
- **Min-Heap** chứa các dự án theo mức vốn (`Capital`) tăng dần.
- **Max-Heap** chứa các dự án theo lợi nhuận (`Profits`) lớn nhất.
- Bỏ tất cả dự án vào Min-Heap.
- Lặp lại `k` lần: chuyển tất cả dự án từ Min-Heap có vốn <= `W` hiện tại sang Max-Heap.
- Lấy dự án có lợi nhuận lớn nhất từ Max-Heap thực hiện và tăng vốn `W`.

### Mã nguồn Java
```java
class Solution {
    public int findMaximizedCapital(int k, int w, int[] profits, int[] capital) {
        int n = profits.length;
        PriorityQueue<int[]> minCapital = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));
        PriorityQueue<Integer> maxProfit = new PriorityQueue<>(Collections.reverseOrder());
        
        for (int i = 0; i < n; i++) {
            minCapital.offer(new int[]{capital[i], profits[i]});
        }
        
        for (int i = 0; i < k; i++) {
            while (!minCapital.isEmpty() && minCapital.peek()[0] <= w) {
                maxProfit.offer(minCapital.poll()[1]);
            }
            
            if (maxProfit.isEmpty()) {
                break;
            }
            
            w += maxProfit.poll();
        }
        
        return w;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: `O(N log N + K log N)`.
- **Không gian (Space)**: `O(N)`.

## 4. Construct Target Array With Multiple Sums (LeetCode 1354)
### Đề bài chi tiết
Bạn có một mảng khởi tạo `[1, 1, ..., 1]`. Tại mỗi bước, bạn có thể chọn một phần tử trong mảng và thay nó bằng tổng của tất cả các phần tử. Cho mảng `target`, xác định xem bạn có thể biến mảng khởi tạo thành `target` hay không.

### Phân tích thuật toán
Thay vì tiến tới (từ `1` đến `target`), ta làm ngược lại. Phần tử lớn nhất trong mảng hiện tại phải là giá trị vừa được cập nhật. Ta dùng Max-Heap để lấy phần tử lớn nhất và tìm giá trị cũ của nó trước khi được cộng với tổng các phần tử còn lại. Phép chia lấy dư (`modulo`) giúp xử lý quá trình lặp lại trừ nhiều lần hiệu quả.

### Mã nguồn Java
```java
class Solution {
    public boolean isPossible(int[] target) {
        if (target.length == 1) return target[0] == 1;
        
        PriorityQueue<Long> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        long totalSum = 0;
        
        for (int num : target) {
            maxHeap.offer((long) num);
            totalSum += num;
        }
        
        while (maxHeap.peek() > 1) {
            long largest = maxHeap.poll();
            long rest = totalSum - largest;
            
            if (rest == 1) return true; // Đặc biệt cho mảng 2 phần tử
            if (rest == 0 || largest <= rest) return false;
            
            long prev = largest % rest;
            if (prev == 0) return false;
            
            maxHeap.offer(prev);
            totalSum = rest + prev;
        }
        
        return true;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: `O(N log N + log(max_val) * log N)`.
- **Không gian (Space)**: `O(N)`.

## 5. Find Right Interval (LeetCode 436)
### Đề bài chi tiết
Cho mảng các khoảng `intervals`. Tìm chỉ số của khoảng có phần `start` nhỏ nhất thỏa mãn `start >= end` của khoảng hiện tại. 

### Phân tích thuật toán
Bài toán có thể giải bằng Two Heaps (hoặc Sắp xếp + Nhị phân/TreeMap).
- Dùng **Max-Heap** cho điểm `end` của các khoảng (hoặc Min-Heap theo yêu cầu).
Cách giải thường gặp nhất bằng PriorityQueue là đưa mảng các `(start, index)` vào **Min-Heap**, và vòng lặp các phần tử tìm interval. Bài này không hoàn toàn "Two Heaps" truyền thống nhưng chia 2 queues hoặc Heap kết hợp sorting.
*Ở đây minh họa dùng TreeMap (cũng là cấu trúc dữ liệu duy trì tính sắp xếp như Heap).*

### Mã nguồn Java
```java
class Solution {
    public int[] findRightInterval(int[][] intervals) {
        TreeMap<Integer, Integer> startMap = new TreeMap<>();
        int n = intervals.length;
        for (int i = 0; i < n; i++) {
            startMap.put(intervals[i][0], i);
        }
        
        int[] result = new int[n];
        for (int i = 0; i < n; i++) {
            Map.Entry<Integer, Integer> entry = startMap.ceilingEntry(intervals[i][1]);
            result[i] = (entry != null) ? entry.getValue() : -1;
        }
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: `O(N log N)`.
- **Không gian (Space)**: `O(N)`.

## 6. Maximum Performance of a Team (LeetCode 1383)
### Đề bài chi tiết
Chọn ra nhiều nhất `k` kỹ sư từ `n` kỹ sư để tối đa hóa hiệu suất. Hiệu suất được tính bằng tổng `speed` nhân với `efficiency` nhỏ nhất trong nhóm.

### Phân tích thuật toán
- Sắp xếp các kỹ sư giảm dần theo `efficiency`.
- Duyệt qua từng kỹ sư, coi kỹ sư hiện tại là người có `efficiency` nhỏ nhất trong nhóm (vì đã sắp xếp giảm dần).
- Sử dụng một **Min-Heap** (chứa `speed`) để duy trì `k` người có tốc độ lớn nhất.
- Tổng `speed` được duy trì khi thêm vào, nếu số lượng > `k`, lấy phần tử nhỏ nhất khỏi Heap và trừ khỏi tổng `speed`.

### Mã nguồn Java
```java
class Solution {
    public int maxPerformance(int n, int[] speed, int[] efficiency, int k) {
        int[][] engineers = new int[n][2];
        for (int i = 0; i < n; i++) {
            engineers[i] = new int[]{efficiency[i], speed[i]};
        }
        
        Arrays.sort(engineers, (a, b) -> b[0] - a[0]);
        
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        long maxPerformance = 0;
        long totalSpeed = 0;
        
        for (int i = 0; i < n; i++) {
            int currEfficiency = engineers[i][0];
            int currSpeed = engineers[i][1];
            
            minHeap.offer(currSpeed);
            totalSpeed += currSpeed;
            
            if (minHeap.size() > k) {
                totalSpeed -= minHeap.poll();
            }
            
            maxPerformance = Math.max(maxPerformance, totalSpeed * currEfficiency);
        }
        
        return (int) (maxPerformance % 1_000_000_007);
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: `O(N log N + N log K)`.
- **Không gian (Space)**: `O(N + K)`.

## 7. Minimum Number of Refueling Stops (LeetCode 871)
### Đề bài chi tiết
Một xe ô tô cần đi quãng đường `target` với xăng ban đầu là `startFuel`. Trên đường có các trạm xăng có vị trí và lượng xăng nhất định. Tìm số lần dừng đổ xăng tối thiểu để tới được đích.

### Phân tích thuật toán
- Đi càng xa càng tốt bằng số xăng hiện có.
- Lưu lại tất cả các trạm xăng đi ngang qua vào một **Max-Heap** (ưu tiên trạm có nhiều xăng nhất).
- Khi hết xăng mà chưa tới đích, lấy trạm có nhiều xăng nhất từ Max-Heap để bổ sung (tương đương với việc quay lại trạm đó đổ xăng trong quá khứ).

### Mã nguồn Java
```java
class Solution {
    public int minRefuelStops(int target, int startFuel, int[][] stations) {
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        int stops = 0;
        int maxReach = startFuel;
        int i = 0;
        int n = stations.length;
        
        while (maxReach < target) {
            while (i < n && stations[i][0] <= maxReach) {
                maxHeap.offer(stations[i][1]);
                i++;
            }
            
            if (maxHeap.isEmpty()) {
                return -1;
            }
            
            maxReach += maxHeap.poll();
            stops++;
        }
        
        return stops;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: `O(N log N)`.
- **Không gian (Space)**: `O(N)`.

## 8. Process Tasks Using Servers (LeetCode 1882)
### Đề bài chi tiết
Có mảng các `servers` (trọng số) và mảng các `tasks` (thời gian xử lý). Nhiệm vụ xuất hiện vào các thời điểm tương ứng chỉ số mảng. Phân công task cho server nhàn rỗi có trọng số nhỏ nhất.

### Phân tích thuật toán
Sử dụng **Hai Heaps**:
1. `freeServers`: Min-Heap chứa các server đang nhàn rỗi, ưu tiên `(weight, index)`.
2. `busyServers`: Min-Heap chứa các server đang bận, ưu tiên theo thời gian sẽ hoàn thành task `(endTime, weight, index)`.
Khi thời gian chạy, liên tục đưa các server từ `busyServers` sang `freeServers`.

### Mã nguồn Java
```java
class Solution {
    public int[] assignTasks(int[] servers, int[] tasks) {
        PriorityQueue<int[]> freeServers = new PriorityQueue<>((a, b) -> {
            if (a[0] != b[0]) return Integer.compare(a[0], b[0]);
            return Integer.compare(a[1], b[1]);
        });
        
        PriorityQueue<int[]> busyServers = new PriorityQueue<>((a, b) -> {
            if (a[0] != b[0]) return Integer.compare(a[0], b[0]);
            if (a[1] != b[1]) return Integer.compare(a[1], b[1]);
            return Integer.compare(a[2], b[2]);
        });
        
        for (int i = 0; i < servers.length; i++) {
            freeServers.offer(new int[]{servers[i], i});
        }
        
        int[] result = new int[tasks.length];
        int time = 0;
        
        for (int i = 0; i < tasks.length; i++) {
            time = Math.max(time, i);
            
            if (freeServers.isEmpty()) {
                time = busyServers.peek()[0];
            }
            
            while (!busyServers.isEmpty() && busyServers.peek()[0] <= time) {
                int[] server = busyServers.poll();
                freeServers.offer(new int[]{server[1], server[2]});
            }
            
            int[] bestServer = freeServers.poll();
            result[i] = bestServer[1];
            busyServers.offer(new int[]{time + tasks[i], bestServer[0], bestServer[1]});
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: `O((M + N) log M)` với `M` là số server, `N` là số task.
- **Không gian (Space)**: `O(M)`.

## 9. Course Schedule III (LeetCode 630)
### Đề bài chi tiết
Cho mảng `courses`, với `courses[i] = [duration, lastDay]`. Bạn cần hoàn thành tối đa số lượng khóa học sao cho mỗi khóa học hoàn thành trước `lastDay`.

### Phân tích thuật toán
- Sắp xếp các khóa học theo `lastDay` tăng dần.
- Duyệt qua từng khóa học, tính tổng thời gian đã dùng.
- Thêm khóa học vào **Max-Heap** chứa thời lượng (duration).
- Nếu tổng thời gian > `lastDay` của khóa hiện tại, lấy khóa học có duration lớn nhất trong Max-Heap loại bỏ (có thể chính là khóa vừa vào), để tiết kiệm thời gian nhất cho tương lai.

### Mã nguồn Java
```java
class Solution {
    public int scheduleCourse(int[][] courses) {
        Arrays.sort(courses, (a, b) -> a[1] - b[1]);
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        
        int time = 0;
        for (int[] course : courses) {
            time += course[0];
            maxHeap.offer(course[0]);
            
            if (time > course[1]) {
                time -= maxHeap.poll();
            }
        }
        
        return maxHeap.size();
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: `O(N log N)`.
- **Không gian (Space)**: `O(N)`.

## 10. Minimum Cost to Hire K Workers (LeetCode 857)
### Đề bài chi tiết
Mỗi công nhân có `quality` và `wage`. Cần thuê `k` công nhân sao cho tổng `quality` * max(tỉ lệ wage/quality) là nhỏ nhất. Mọi công nhân phải được trả lương tương ứng với chất lượng làm việc.

### Phân tích thuật toán
- Tỉ lệ trả lương = `wage / quality`.
- Sắp xếp các công nhân theo tỉ lệ tăng dần.
- Dùng **Max-Heap** lưu trữ `quality` của các công nhân đang xét.
- Khi số người > `k`, loại bỏ người có `quality` lớn nhất (để tổng `quality` nhỏ nhất).
- Tính lại mức phí. Mức phí sẽ bằng `totalQuality` nhân với tỉ lệ trả lương hiện tại.

### Mã nguồn Java
```java
class Solution {
    public double mincostToHireWorkers(int[] quality, int[] wage, int k) {
        int n = quality.length;
        double[][] workers = new double[n][2];
        for (int i = 0; i < n; i++) {
            workers[i] = new double[]{(double) wage[i] / quality[i], (double) quality[i]};
        }
        
        Arrays.sort(workers, (a, b) -> Double.compare(a[0], b[0]));
        
        PriorityQueue<Double> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        double minCost = Double.MAX_VALUE;
        double totalQuality = 0;
        
        for (double[] worker : workers) {
            totalQuality += worker[1];
            maxHeap.offer(worker[1]);
            
            if (maxHeap.size() > k) {
                totalQuality -= maxHeap.poll();
            }
            
            if (maxHeap.size() == k) {
                minCost = Math.min(minCost, totalQuality * worker[0]);
            }
        }
        
        return minCost;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: `O(N log N)`.
- **Không gian (Space)**: `O(N)`.

## 11. K-th Smallest Element in a Sorted Matrix (LeetCode 378)
### Đề bài chi tiết
Cho ma trận vuông `n x n` được sắp xếp theo từng hàng và từng cột theo thứ tự tăng dần. Tìm phần tử nhỏ thứ `k` trong ma trận.

### Phân tích thuật toán
Sử dụng **Min-Heap** để lưu trữ các phần tử cùng với vị trí của chúng (hàng, cột). 
- Bắt đầu bằng cách thêm phần tử đầu tiên của mỗi hàng vào Min-Heap. 
- Ở mỗi bước, lấy phần tử nhỏ nhất ra khỏi Min-Heap (lặp `k-1` lần) và thêm phần tử tiếp theo trong cùng hàng vào Heap. 
- Sau `k-1` bước, phần tử trên đỉnh Heap chính là phần tử nhỏ thứ `k`. Mặc dù bài này không dùng hẳn "Two Heaps", nhưng cấu trúc Min-Heap/K-way Merge có thể giải quyết các bài toán tìm kiếm giá trị nhỏ/lớn thứ K rất tối ưu.

### Mã nguồn Java
```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> a[0] - b[0]);
        
        for (int r = 0; r < Math.min(n, k); r++) {
            minHeap.offer(new int[]{matrix[r][0], r, 0});
        }
        
        for (int i = 0; i < k - 1; i++) {
            int[] current = minHeap.poll();
            int r = current[1];
            int c = current[2];
            
            if (c + 1 < n) {
                minHeap.offer(new int[]{matrix[r][c + 1], r, c + 1});
            }
        }
        
        return minHeap.poll()[0];
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(K log(min(N, K)))`.
- **Không gian (Space Complexity)**: `O(min(N, K))`.

## 12. Kth Largest Element in a Stream (LeetCode 703)
### Đề bài chi tiết
Thiết kế một lớp để tìm phần tử lớn thứ `k` trong một luồng số. Lớp cần triển khai phương thức `add(int val)` trả về phần tử lớn thứ `k` hiện tại trong luồng.

### Phân tích thuật toán
Sử dụng **Min-Heap** có kích thước tối đa là `k`. 
- Khi thêm một phần tử mới, nếu kích thước Heap nhỏ hơn `k`, ta thêm trực tiếp. 
- Nếu lớn hơn hoặc bằng `k` và phần tử mới lớn hơn phần tử đỉnh Heap, ta pop phần tử đỉnh (tức là phần tử nhỏ nhất trong k phần tử lớn nhất) ra và thêm phần tử mới vào. 
- Nhờ vậy, phần tử trên đỉnh của Min-Heap luôn là phần tử nhỏ nhất trong `k` phần tử lớn nhất, tức là phần tử lớn thứ `k`.

### Mã nguồn Java
```java
class KthLargest {
    private PriorityQueue<Integer> minHeap;
    private int k;

    public KthLargest(int k, int[] nums) {
        this.k = k;
        this.minHeap = new PriorityQueue<>();
        for (int num : nums) {
            add(num);
        }
    }
    
    public int add(int val) {
        if (minHeap.size() < k) {
            minHeap.offer(val);
        } else if (val > minHeap.peek()) {
            minHeap.poll();
            minHeap.offer(val);
        }
        return minHeap.peek();
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(N log K)` cho hàm khởi tạo, `O(log K)` cho mỗi thao tác `add`.
- **Không gian (Space Complexity)**: `O(K)` để duy trì Heap chứa `K` phần tử.

## 13. Sort Characters By Frequency (LeetCode 451)
### Đề bài chi tiết
Cho một chuỗi `s`, sắp xếp chuỗi đó theo thứ tự giảm dần dựa trên tần suất xuất hiện của các ký tự. 

### Phân tích thuật toán
- Đếm tần suất xuất hiện của mỗi ký tự bằng `HashMap`. 
- Sử dụng **Max-Heap** để lưu trữ các Map.Entry dựa trên giá trị (tần suất) của chúng giảm dần. 
- Lấy lần lượt các phần tử ra từ Max-Heap, lấy ký tự nhân lên theo tần suất tương ứng và nối vào chuỗi kết quả.

### Mã nguồn Java
```java
class Solution {
    public String frequencySort(String s) {
        Map<Character, Integer> freqMap = new HashMap<>();
        for (char c : s.toCharArray()) {
            freqMap.put(c, freqMap.getOrDefault(c, 0) + 1);
        }
        
        PriorityQueue<Map.Entry<Character, Integer>> maxHeap = 
            new PriorityQueue<>((a, b) -> b.getValue() - a.getValue());
            
        maxHeap.addAll(freqMap.entrySet());
        
        StringBuilder sb = new StringBuilder();
        while (!maxHeap.isEmpty()) {
            Map.Entry<Character, Integer> entry = maxHeap.poll();
            for (int i = 0; i < entry.getValue(); i++) {
                sb.append(entry.getKey());
            }
        }
        
        return sb.toString();
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(N log M)` với `N` là độ dài chuỗi và `M` là số lượng ký tự khác nhau. Do giới hạn số lượng ký tự, có thể coi là `O(N)`.
- **Không gian (Space Complexity)**: `O(M)`.

## 14. Top K Frequent Elements (LeetCode 347)
### Đề bài chi tiết
Cho một mảng số nguyên `nums` và một số nguyên `k`, trả về `k` phần tử xuất hiện nhiều nhất. Có thể trả về kết quả theo bất kỳ thứ tự nào.

### Phân tích thuật toán
- Tương tự như sắp xếp theo tần suất, đầu tiên sử dụng `HashMap` để đếm tần suất.
- Sau đó, dùng **Min-Heap** kích thước `k` để duy trì `k` phần tử có tần suất cao nhất (thay vì Max-Heap để tối ưu bộ nhớ nếu số phần tử khác nhau rất lớn). 
- Khi số phần tử trong Heap vượt quá `k`, ta bỏ phần tử có tần suất thấp nhất ra khỏi Min-Heap. Cuối cùng, đỉnh Min-Heap luôn chứa top K.

### Mã nguồn Java
```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freqMap = new HashMap<>();
        for (int num : nums) {
            freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);
        }
        
        PriorityQueue<Integer> minHeap = new PriorityQueue<>(
            (n1, n2) -> freqMap.get(n1) - freqMap.get(n2)
        );
        
        for (int num : freqMap.keySet()) {
            minHeap.offer(num);
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }
        
        int[] top = new int[k];
        for (int i = k - 1; i >= 0; i--) {
            top[i] = minHeap.poll();
        }
        
        return top;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(N log K)`.
- **Không gian (Space Complexity)**: `O(N + K)`.

## 15. Find K Pairs with Smallest Sums (LeetCode 373)
### Đề bài chi tiết
Cho hai mảng số nguyên `nums1` và `nums2` đã sắp xếp theo thứ tự tăng dần và một số nguyên `k`. Tìm `k` cặp `(u, v)` (trong đó `u` từ `nums1`, `v` từ `nums2`) có tổng nhỏ nhất.

### Phân tích thuật toán
- Sử dụng **Min-Heap** lưu các mảng có định dạng `[giá_trị_nums1, giá_trị_nums2, chỉ_số_của_nums2]`. 
- Ban đầu, đưa `k` phần tử đầu tiên của `nums1` kết hợp với phần tử đầu tiên của `nums2` vào Min-Heap. 
- Sau đó, lặp lại `k` lần: lấy cặp có tổng nhỏ nhất từ Min-Heap ra làm kết quả.
- Dựa trên chỉ số của `nums2` đã lấy, thêm cặp tiếp theo (cùng giá trị `nums1`, phần tử kế tiếp của `nums2`) vào Min-Heap.

### Mã nguồn Java
```java
class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> result = new ArrayList<>();
        if (nums1.length == 0 || nums2.length == 0 || k == 0) return result;
        
        PriorityQueue<int[]> minHeap = new PriorityQueue<>(
            (a, b) -> (a[0] + a[1]) - (b[0] + b[1])
        );
        
        for (int i = 0; i < nums1.length && i < k; i++) {
            minHeap.offer(new int[]{nums1[i], nums2[0], 0});
        }
        
        while (k-- > 0 && !minHeap.isEmpty()) {
            int[] current = minHeap.poll();
            result.add(Arrays.asList(current[0], current[1]));
            
            int nextIndex = current[2] + 1;
            if (nextIndex < nums2.length) {
                minHeap.offer(new int[]{current[0], nums2[nextIndex], nextIndex});
            }
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(K log K)`.
- **Không gian (Space Complexity)**: `O(K)` cho Min-Heap.

## 16. Rearrange String k Distance Apart (LeetCode 358)
### Đề bài chi tiết
Cho một chuỗi không rỗng `s` và số nguyên `k`, hãy sắp xếp lại chuỗi sao cho cùng một ký tự phải cách nhau ít nhất `k` vị trí. Nếu không thể sắp xếp lại, trả về chuỗi rỗng.

### Phân tích thuật toán
- Dùng HashMap đếm tần suất các ký tự.
- Sử dụng **Max-Heap** để luôn ưu tiên chọn ký tự xuất hiện nhiều nhất. 
- Sử dụng một hàng đợi (Queue) đóng vai trò "cooldown" (thời gian hồi chiêu) với kích thước `k`. Sau khi sử dụng 1 ký tự, giảm tần suất của nó và cho vào Queue. 
- Nếu Queue đạt kích thước `k`, lấy phần tử đầu Queue ra và nếu tần suất của nó còn lớn hơn 0, thêm lại vào Max-Heap để tái sử dụng. 
- Cuối cùng, nếu độ dài chuỗi kết quả nhỏ hơn độ dài chuỗi ban đầu, chứng tỏ không thể tạo thành chuỗi hợp lệ.

### Mã nguồn Java
```java
class Solution {
    public String rearrangeString(String s, int k) {
        if (k == 0) return s;
        
        Map<Character, Integer> freqMap = new HashMap<>();
        for (char c : s.toCharArray()) {
            freqMap.put(c, freqMap.getOrDefault(c, 0) + 1);
        }
        
        PriorityQueue<Map.Entry<Character, Integer>> maxHeap = new PriorityQueue<>(
            (a, b) -> b.getValue() - a.getValue()
        );
        maxHeap.addAll(freqMap.entrySet());
        
        Queue<Map.Entry<Character, Integer>> queue = new LinkedList<>();
        StringBuilder sb = new StringBuilder();
        
        while (!maxHeap.isEmpty()) {
            Map.Entry<Character, Integer> current = maxHeap.poll();
            sb.append(current.getKey());
            current.setValue(current.getValue() - 1);
            queue.offer(current);
            
            if (queue.size() >= k) {
                Map.Entry<Character, Integer> front = queue.poll();
                if (front.getValue() > 0) {
                    maxHeap.offer(front);
                }
            }
        }
        
        return sb.length() == s.length() ? sb.toString() : "";
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(N log A)` với `N` là chiều dài chuỗi, `A` là số ký tự phân biệt. Giới hạn `A <= 26`, nên thời gian là `O(N)`.
- **Không gian (Space Complexity)**: `O(A)` (khoảng `O(1)` với giới hạn alphabet).

## 17. Task Scheduler (LeetCode 621)
### Đề bài chi tiết
Cho mảng `tasks` đại diện cho các công việc và số nguyên `n` đại diện cho khoảng thời gian cooldown (làm mát) tối thiểu giữa hai công việc giống nhau. Mỗi khoảng thời gian CPU có thể thực hiện một công việc hoặc nghỉ. Tính số đơn vị thời gian tối thiểu để hoàn thành tất cả công việc.

### Phân tích thuật toán
- Dùng **Max-Heap** để lưu số lượng công việc còn lại chưa xử lý (tần suất). 
- Trong mỗi chu kỳ có độ dài `n + 1`, lấy tối đa `n + 1` công việc từ Max-Heap để xử lý. Các công việc này sau đó được giảm số lượng và nếu còn dư, đưa tạm vào một danh sách chờ. 
- Sau khi kết thúc chu kỳ xử lý, đưa các công việc trong danh sách chờ trở lại vào Max-Heap. 
- Thời gian cộng thêm sẽ là `n + 1` cho mỗi chu kỳ hoàn thành hoặc số lượng công việc thực sự đã xử lý trong chu kỳ cuối cùng (khi Heap rỗng).

### Mã nguồn Java
```java
class Solution {
    public int leastInterval(char[] tasks, int n) {
        int[] frequencies = new int[26];
        for (char task : tasks) {
            frequencies[task - 'A']++;
        }
        
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        for (int freq : frequencies) {
            if (freq > 0) {
                maxHeap.offer(freq);
            }
        }
        
        int intervals = 0;
        while (!maxHeap.isEmpty()) {
            List<Integer> waitList = new ArrayList<>();
            int k = n + 1; // một chu kỳ
            
            while (k > 0 && !maxHeap.isEmpty()) {
                int currentFreq = maxHeap.poll();
                if (currentFreq > 1) {
                    waitList.add(currentFreq - 1);
                }
                intervals++;
                k--;
            }
            
            for (int freq : waitList) {
                maxHeap.offer(freq);
            }
            
            if (!maxHeap.isEmpty()) {
                intervals += k; // Cộng thêm thời gian nghỉ (idle) nếu chu kỳ chưa đủ
            }
        }
        
        return intervals;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(N)`. Mặc dù dùng PriorityQueue, số phần tử tối đa trong Heap là 26. Do đó chi phí là hằng số.
- **Không gian (Space Complexity)**: `O(1)`.

## 18. Maximum Number of Events That Can Be Attended (LeetCode 1353)
### Đề bài chi tiết
Cho một mảng `events` trong đó `events[i] = [startDay_i, endDay_i]`. Bạn có thể tham gia một sự kiện bất kỳ vào một ngày thuộc khoảng thời gian diễn ra của sự kiện đó. Tuy nhiên, mỗi ngày chỉ có thể tham gia 1 sự kiện. Tìm số lượng sự kiện tối đa bạn có thể tham gia.

### Phân tích thuật toán
- Sắp xếp mảng `events` theo ngày bắt đầu `startDay`. 
- Sử dụng **Min-Heap** để lưu trữ `endDay` của các sự kiện đang diễn ra. 
- Bắt đầu từ ngày diễn ra sự kiện đầu tiên. Mỗi ngày, thêm tất cả sự kiện bắt đầu vào ngày đó vào Min-Heap. Xóa các sự kiện đã kết thúc trong quá khứ (`endDay < currentDay`). 
- Sau đó, nếu Heap không rỗng, tham gia sự kiện có `endDay` sớm nhất (nằm ở đỉnh Min-Heap) và tăng biến đếm.

### Mã nguồn Java
```java
class Solution {
    public int maxEvents(int[][] events) {
        Arrays.sort(events, (a, b) -> a[0] - b[0]);
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        int maxEvents = 0;
        int currentDay = 0;
        int i = 0;
        int n = events.length;
        
        while (i < n || !minHeap.isEmpty()) {
            if (minHeap.isEmpty()) {
                currentDay = events[i][0];
            }
            
            while (i < n && events[i][0] <= currentDay) {
                minHeap.offer(events[i][1]);
                i++;
            }
            
            minHeap.poll(); // Tham gia sự kiện ưu tiên kết thúc sớm nhất
            maxEvents++;
            currentDay++;
            
            while (!minHeap.isEmpty() && minHeap.peek() < currentDay) {
                minHeap.poll(); // Bỏ đi các sự kiện đã quá hạn
            }
        }
        
        return maxEvents;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(N log N)` do sắp xếp và thao tác Push/Pop với Min-Heap.
- **Không gian (Space Complexity)**: `O(N)`.

## 19. Single-Threaded CPU (LeetCode 1834)
### Đề bài chi tiết
Cho mảng `tasks` với `tasks[i] = [enqueueTime, processingTime]`. CPU chỉ xử lý một tác vụ tại một thời điểm. Tại mỗi thời điểm, nếu có nhiều tác vụ sẵn sàng, CPU sẽ ưu tiên tác vụ có `processingTime` ngắn nhất. Nếu có nhiều tác vụ cùng thời gian xử lý, chọn tác vụ có `index` ban đầu nhỏ hơn. Trả về thứ tự các tác vụ được thực hiện.

### Phân tích thuật toán
- Lưu trữ `index` ban đầu cùng thông tin của tác vụ và sắp xếp các tác vụ theo `enqueueTime`. 
- Sử dụng **Min-Heap** ưu tiên tác vụ có `(processingTime, index)` nhỏ nhất. 
- Khởi tạo mốc thời gian `time` = 0. Nếu `time` nhỏ hơn `enqueueTime` của tác vụ kế tiếp và Min-Heap rỗng, nhảy thẳng `time` đến `enqueueTime` đó. 
- Đưa tất cả các tác vụ đã sẵn sàng (`enqueueTime <= time`) vào Min-Heap. 
- Lấy tác vụ ở đỉnh Min-Heap, thực hiện nó, và cộng dồn `processingTime` vào `time`.

### Mã nguồn Java
```java
class Solution {
    public int[] getOrder(int[][] tasks) {
        int n = tasks.length;
        int[][] extTasks = new int[n][3];
        for (int i = 0; i < n; i++) {
            extTasks[i][0] = tasks[i][0];
            extTasks[i][1] = tasks[i][1];
            extTasks[i][2] = i; // Giữ nguyên index ban đầu
        }
        
        Arrays.sort(extTasks, (a, b) -> Integer.compare(a[0], b[0]));
        
        PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> {
            if (a[1] != b[1]) return Integer.compare(a[1], b[1]);
            return Integer.compare(a[2], b[2]);
        });
        
        int[] result = new int[n];
        int resultIdx = 0;
        int taskIdx = 0;
        long time = 0;
        
        while (resultIdx < n) {
            if (minHeap.isEmpty() && time < extTasks[taskIdx][0]) {
                time = extTasks[taskIdx][0];
            }
            
            while (taskIdx < n && extTasks[taskIdx][0] <= time) {
                minHeap.offer(extTasks[taskIdx]);
                taskIdx++;
            }
            
            int[] nextTask = minHeap.poll();
            time += nextTask[1];
            result[resultIdx++] = nextTask[2];
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(N log N)`.
- **Không gian (Space Complexity)**: `O(N)` để lưu thông tin mở rộng của các task.

## 20. Furthest Building You Can Reach (LeetCode 1642)
### Đề bài chi tiết
Bạn cần di chuyển qua các tòa nhà có chiều cao cho trước. Bạn được cấp một số lượng gạch (`bricks`) và số thang (`ladders`). Nếu tòa nhà tiếp theo cao hơn, bạn phải dùng số gạch bằng chênh lệch độ cao, hoặc sử dụng 1 cái thang. Tìm vị trí tòa nhà xa nhất (index lớn nhất) bạn có thể đến được.

### Phân tích thuật toán
- Thay vì quyết định trước dùng gạch hay thang, ta tối ưu hóa bằng cách: **luôn dùng thang cho các chênh lệch lớn nhất**. 
- Sử dụng **Min-Heap** lưu các bước nhảy (chênh lệch) mà ta đã dùng thang. 
- Trong mỗi bước leo lên, đẩy độ chênh lệch vào Min-Heap. Nếu kích thước Min-Heap vượt quá số lượng thang `ladders`, điều đó có nghĩa ta phải chuyển bước nhảy nhỏ nhất trong Heap thành dùng gạch. 
- Lấy phần tử nhỏ nhất khỏi Heap, trừ nó vào số `bricks`. Nếu số `bricks` nhỏ hơn 0, ta không thể nhảy tiếp và kết thúc ở vị trí hiện tại.

### Mã nguồn Java
```java
class Solution {
    public int furthestBuilding(int[] heights, int bricks, int ladders) {
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        
        for (int i = 0; i < heights.length - 1; i++) {
            int climb = heights[i + 1] - heights[i];
            
            if (climb > 0) {
                minHeap.offer(climb);
                
                // Nếu vượt quá số thang, lấy chênh lệch nhỏ nhất ra dùng gạch bù vào
                if (minHeap.size() > ladders) {
                    bricks -= minHeap.poll();
                }
                
                // Nếu không đủ gạch, dừng lại ở vị trí i
                if (bricks < 0) {
                    return i;
                }
            }
        }
        
        return heights.length - 1;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(N log L)`, với `N` là số tòa nhà, `L` là số lượng thang.
- **Không gian (Space Complexity)**: `O(L)` cho kích thước Min-Heap.

## 21. Seat Reservation Manager (LeetCode 1845)
### Đề bài chi tiết
Thiết kế một hệ thống quản lý chỗ ngồi từ `1` đến `n`. Hệ thống cần hỗ trợ:
- `reserve()`: Trả về số ghế nhỏ nhất còn trống và đánh dấu nó là đã được đặt.
- `unreserve(int seatNumber)`: Hủy đặt ghế có số `seatNumber` và đánh dấu nó là còn trống.

### Phân tích thuật toán
- Sử dụng **Min-Heap** để lưu trữ các ghế trống.
- Ban đầu, thay vì thêm tất cả `n` ghế vào Min-Heap (chi phí `O(N)` về không gian và thời gian), ta có thể sử dụng một biến `marker` để theo dõi ghế trống lớn nhất chưa từng được đặt.
- Khi gọi `reserve()`, nếu Min-Heap không rỗng, ta lấy ghế nhỏ nhất từ Min-Heap. Nếu Min-Heap rỗng, ta lấy `marker` và tăng `marker` lên.
- Khi gọi `unreserve(seatNumber)`, ta chỉ việc đưa `seatNumber` vào Min-Heap.

### Mã nguồn Java
```java
class SeatManager {
    private PriorityQueue<Integer> minHeap;
    private int marker;

    public SeatManager(int n) {
        minHeap = new PriorityQueue<>();
        marker = 1;
    }
    
    public int reserve() {
        if (!minHeap.isEmpty()) {
            return minHeap.poll();
        }
        return marker++;
    }
    
    public void unreserve(int seatNumber) {
        minHeap.offer(seatNumber);
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(1)` khởi tạo. `O(log K)` cho mỗi thao tác với `K` là số ghế đã bị hủy đặt (`unreserve`).
- **Không gian (Space Complexity)**: `O(K)` cho Min-Heap.

## 22. Last Stone Weight (LeetCode 1046)
### Đề bài chi tiết
Cho một mảng các viên đá có trọng lượng `stones`. Mỗi lượt, chọn ra 2 viên đá nặng nhất và đập chúng vào nhau.
- Nếu `x == y`, cả 2 viên bị phá hủy.
- Nếu `x != y`, viên `x` bị phá hủy, viên `y` còn lại trọng lượng `y - x`.
Quá trình lặp lại đến khi còn lại nhiều nhất 1 viên đá. Trả về trọng lượng viên đá đó hoặc `0` nếu không còn viên nào.

### Phân tích thuật toán
- Sử dụng **Max-Heap** để luôn dễ dàng lấy ra được 2 viên đá nặng nhất.
- Đưa tất cả viên đá ban đầu vào Max-Heap.
- Lặp lại chừng nào Heap còn ít nhất 2 viên: Lấy 2 viên ra, đập vào nhau. Nếu có phần dư lớn hơn `0`, đưa phần dư quay lại Max-Heap.
- Cuối cùng kiểm tra Heap, nếu còn phần tử thì trả về phần tử đó, nếu không thì trả về `0`.

### Mã nguồn Java
```java
class Solution {
    public int lastStoneWeight(int[] stones) {
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        for (int stone : stones) {
            maxHeap.offer(stone);
        }
        
        while (maxHeap.size() > 1) {
            int y = maxHeap.poll();
            int x = maxHeap.poll();
            
            if (y > x) {
                maxHeap.offer(y - x);
            }
        }
        
        return maxHeap.isEmpty() ? 0 : maxHeap.peek();
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(N log N)` để xây dựng Heap và tối đa `N` thao tác rút/thêm.
- **Không gian (Space Complexity)**: `O(N)` để lưu trữ Heap.

## 23. The Skyline Problem (LeetCode 218)
### Đề bài chi tiết
Cho danh sách các tòa nhà với thông tin `[left, right, height]`. Tìm "đường chân trời" (skyline), tức là danh sách các điểm ngoặt (điểm mà đỉnh trên cùng của tòa nhà thay đổi độ cao) theo thứ tự tọa độ x tăng dần.

### Phân tích thuật toán
- Ta có thể coi mỗi tòa nhà bao gồm 2 sự kiện: điểm bắt đầu `[left, height]` và điểm kết thúc `[right, height]`. 
- Để dễ phân biệt, ta lưu điểm bắt đầu với chiều cao mang dấu âm: `[left, -height]`.
- Sắp xếp các sự kiện theo tọa độ x tăng dần. Nếu trùng x, sắp xếp theo chiều cao (cách lưu dấu âm giúp xử lý đúng logic khi 2 tòa nhà bắt đầu/kết thúc tại cùng 1 điểm x).
- Sử dụng **TreeMap** (hoặc Max-Heap có lazy deletion) để theo dõi chiều cao hiện tại của các tòa nhà. Khi gặp điểm bắt đầu, thêm chiều cao vào TreeMap. Khi gặp điểm kết thúc, xóa chiều cao đó.
- Nếu chiều cao lớn nhất (đỉnh) trong TreeMap thay đổi so với đỉnh trước đó, ta ghi nhận một điểm ngoặt vào kết quả.

### Mã nguồn Java
```java
class Solution {
    public List<List<Integer>> getSkyline(int[][] buildings) {
        List<int[]> points = new ArrayList<>();
        for (int[] b : buildings) {
            points.add(new int[]{b[0], -b[2]}); // Bắt đầu: lưu giá trị âm
            points.add(new int[]{b[1], b[2]});  // Kết thúc: lưu giá trị dương
        }
        
        // Sắp xếp: theo tọa độ x. Nếu trùng x, sắp xếp theo chiều cao
        Collections.sort(points, (a, b) -> {
            if (a[0] != b[0]) return a[0] - b[0];
            return a[1] - b[1];
        });
        
        List<List<Integer>> result = new ArrayList<>();
        // TreeMap lưu trữ chiều cao và số lượng tòa nhà có chiều cao đó
        TreeMap<Integer, Integer> heightsMap = new TreeMap<>(Collections.reverseOrder());
        heightsMap.put(0, 1); // Mặt đất
        int prevMaxHeight = 0;
        
        for (int[] p : points) {
            if (p[1] < 0) { // Điểm bắt đầu
                heightsMap.put(-p[1], heightsMap.getOrDefault(-p[1], 0) + 1);
            } else { // Điểm kết thúc
                int count = heightsMap.get(p[1]);
                if (count == 1) {
                    heightsMap.remove(p[1]);
                } else {
                    heightsMap.put(p[1], count - 1);
                }
            }
            
            int currMaxHeight = heightsMap.firstKey();
            if (currMaxHeight != prevMaxHeight) {
                result.add(Arrays.asList(p[0], currMaxHeight));
                prevMaxHeight = currMaxHeight;
            }
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(N log N)`, mỗi sự kiện thao tác trên TreeMap mất `O(log N)`.
- **Không gian (Space Complexity)**: `O(N)` để lưu trữ sự kiện và TreeMap.

## 24. Avoid Flood in The City (LeetCode 1488)
### Đề bài chi tiết
Một mảng `rains` chỉ ra hồ nào bị mưa vào ngày `i`. Nếu `rains[i] > 0`, hồ đó sẽ đầy nước. Nếu `rains[i] == 0`, không có mưa và bạn có thể chọn một hồ để tát nước (làm cạn). Nếu một hồ bị mưa hai lần mà chưa được làm cạn, thành phố sẽ bị ngập. Tìm lịch tát nước để tránh ngập lụt.

### Phân tích thuật toán
- Sử dụng `HashMap` để lưu ngày gần nhất mỗi hồ bị mưa.
- Sử dụng `TreeSet` (cấu trúc duy trì thứ tự giống Min-Heap nhưng cho phép tìm kiếm nhị phân phần tử) để lưu danh sách các ngày không mưa (có thể dùng để tát nước).
- Khi hồ `X` bị mưa, nếu hồ này đã đầy (tồn tại trong `HashMap`), ta cần dùng `TreeSet.ceiling()` để tìm ngày tát nước *gần nhất* nhưng *phải sau* ngày mưa trước đó của hồ `X`. 
- Nếu không tìm được ngày phù hợp, sẽ xảy ra ngập. Ngược lại, cập nhật ngày tát nước.

### Mã nguồn Java
```java
class Solution {
    public int[] avoidFlood(int[] rains) {
        int n = rains.length;
        int[] result = new int[n];
        Arrays.fill(result, -1);
        
        Map<Integer, Integer> fullLakes = new HashMap<>(); // Hồ -> Ngày mưa gần nhất
        TreeSet<Integer> dryDays = new TreeSet<>(); // Các ngày không mưa
        
        for (int i = 0; i < n; i++) {
            if (rains[i] == 0) {
                dryDays.add(i);
                result[i] = 1; // Mặc định tát hồ 1 nếu dư ngày
            } else {
                int lake = rains[i];
                if (fullLakes.containsKey(lake)) {
                    // Tìm ngày khô đầu tiên sau ngày mưa trước đó của hồ này
                    Integer dryDay = dryDays.ceiling(fullLakes.get(lake));
                    if (dryDay == null) {
                        return new int[0]; // Tràn nước
                    }
                    result[dryDay] = lake;
                    dryDays.remove(dryDay);
                }
                fullLakes.put(lake, i);
            }
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(N log N)` do hàm `ceiling()` của TreeSet mất `O(log N)`.
- **Không gian (Space Complexity)**: `O(N)`.

## 25. Super Ugly Number (LeetCode 313)
### Đề bài chi tiết
Số "siêu xấu" (Super Ugly Number) là số nguyên dương có tất cả các thừa số nguyên tố thuộc vào mảng `primes` cho trước. Tìm số siêu xấu thứ `n`. (Biết số `1` luôn là số siêu xấu đầu tiên).

### Phân tích thuật toán
- Bài toán này có thể giải bằng cách dùng nhiều con trỏ kết hợp **Min-Heap**.
- Mỗi phần tử trong Min-Heap sẽ lưu một mảng `[giá trị sinh ra, số nguyên tố tạo ra nó, chỉ số của số siêu xấu trước đó]`. 
- Mỗi bước, ta lấy số nhỏ nhất từ Min-Heap (chính là số siêu xấu tiếp theo) đưa vào kết quả. 
- Sau đó, lấy "số nguyên tố gốc" nhân với số siêu xấu ở vị trí tiếp theo rồi lại đẩy vào Heap. Cần chú ý bỏ qua các giá trị trùng lặp.

### Mã nguồn Java
```java
class Solution {
    public int nthSuperUglyNumber(int n, int[] primes) {
        PriorityQueue<long[]> minHeap = new PriorityQueue<>((a, b) -> Long.compare(a[0], b[0]));
        for (int i = 0; i < primes.length; i++) {
            // [giá_trị_mới, số_nguyên_tố, chỉ_số_trong_mảng_ugly]
            minHeap.offer(new long[]{primes[i], primes[i], 0});
        }
        
        int[] ugly = new int[n];
        ugly[0] = 1;
        
        for (int i = 1; i < n; i++) {
            ugly[i] = (int) minHeap.peek()[0];
            
            // Xóa bỏ tất cả các giá trị trùng lặp và tiến con trỏ tương ứng
            while (minHeap.peek()[0] == ugly[i]) {
                long[] curr = minHeap.poll();
                long prime = curr[1];
                int index = (int) curr[2] + 1;
                minHeap.offer(new long[]{prime * ugly[index], prime, index});
            }
        }
        
        return ugly[n - 1];
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(N log K)` với `K` là kích thước mảng `primes`.
- **Không gian (Space Complexity)**: `O(N + K)`.

## 26. Reduce Array Size to The Half (LeetCode 1338)
### Đề bài chi tiết
Bạn có thể chọn một tập hợp các số nguyên từ mảng `arr` và xóa tất cả các lần xuất hiện của các số đó. Tìm kích thước nhỏ nhất của tập hợp cần chọn sao cho mảng còn lại bằng ít nhất một nửa kích thước ban đầu.

### Phân tích thuật toán
- Đầu tiên, đếm tần suất xuất hiện của mỗi số bằng `HashMap`.
- Sau đó, dùng **Max-Heap** để lưu tất cả các tần suất này.
- Lấy liên tục các tần suất lớn nhất từ Max-Heap, cộng dồn vào biến `removed` cho tới khi tổng `removed` lớn hơn hoặc bằng `arr.length / 2`.
- Số lần lấy từ Heap chính là kích thước của tập hợp nhỏ nhất.

### Mã nguồn Java
```java
class Solution {
    public int minSetSize(int[] arr) {
        Map<Integer, Integer> counts = new HashMap<>();
        for (int num : arr) {
            counts.put(num, counts.getOrDefault(num, 0) + 1);
        }
        
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        for (int count : counts.values()) {
            maxHeap.offer(count);
        }
        
        int removed = 0;
        int setSize = 0;
        int target = arr.length / 2;
        
        while (removed < target) {
            removed += maxHeap.poll();
            setSize++;
        }
        
        return setSize;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(N log M)` với `M` là số lượng phần tử khác biệt trong mảng (`M <= N`).
- **Không gian (Space Complexity)**: `O(M)`.

## 27. Reorganize String (LeetCode 767)
### Đề bài chi tiết
Cho chuỗi `s`, sắp xếp lại các ký tự trong chuỗi sao cho không có 2 ký tự nào giống nhau đứng cạnh nhau. Trả về chuỗi kết quả, hoặc chuỗi rỗng nếu không thể làm được.

### Phân tích thuật toán
- Đây là một bài toán giống *Task Scheduler*. Dùng **Max-Heap** để lưu trữ các ký tự dựa trên tần suất xuất hiện.
- Mỗi lần lấy ra ký tự có tần suất cao nhất, nối vào kết quả, giảm tần suất.
- Ký tự vừa sử dụng sẽ được giữ lại trong một biến `prev`. Đến vòng lặp kế tiếp mới được đưa trở lại Heap (như một cooldown) để đảm bảo không đứng cạnh chính nó.

### Mã nguồn Java
```java
class Solution {
    public String reorganizeString(String s) {
        Map<Character, Integer> counts = new HashMap<>();
        for (char c : s.toCharArray()) {
            counts.put(c, counts.getOrDefault(c, 0) + 1);
        }
        
        PriorityQueue<Map.Entry<Character, Integer>> maxHeap = new PriorityQueue<>(
            (a, b) -> b.getValue() - a.getValue()
        );
        maxHeap.addAll(counts.entrySet());
        
        StringBuilder sb = new StringBuilder();
        Map.Entry<Character, Integer> prev = null;
        
        while (!maxHeap.isEmpty()) {
            Map.Entry<Character, Integer> curr = maxHeap.poll();
            sb.append(curr.getKey());
            curr.setValue(curr.getValue() - 1);
            
            // Đưa ký tự trước đó vào lại Heap nếu vẫn còn số lượng
            if (prev != null && prev.getValue() > 0) {
                maxHeap.offer(prev);
            }
            
            prev = curr;
        }
        
        return sb.length() == s.length() ? sb.toString() : "";
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(N log A)` với `A <= 26` là bảng chữ cái, có thể coi là `O(N)`.
- **Không gian (Space Complexity)**: `O(A)` tương đương `O(1)`.

## 28. Find the Kth Smallest Sum of a Matrix With Sorted Rows (LeetCode 1439)
### Đề bài chi tiết
Cho một ma trận `m x n` mà mỗi hàng đều đã được sắp xếp tăng dần. Bạn phải chọn chính xác 1 phần tử từ mỗi hàng để tạo thành một tổng. Tìm tổng nhỏ thứ `k`.

### Phân tích thuật toán
- Tiếp cận bài toán này bằng cách giải quyết từng hàng một. Bắt đầu với hàng đầu tiên, duy trì một danh sách tối đa `k` tổng nhỏ nhất.
- Khi chuyển sang hàng tiếp theo, duyệt qua tất cả các cặp có thể giữa danh sách các tổng nhỏ nhất hiện tại và các phần tử của hàng mới.
- Sử dụng **Max-Heap** có kích thước giới hạn là `k` để giữ lại top `k` tổng nhỏ nhất ở bước đó. 
- Sau khi duyệt qua tất cả các hàng, đỉnh của Max-Heap chính là phần tử lớn nhất trong `k` tổng nhỏ nhất, tức là tổng nhỏ thứ `k`.

### Mã nguồn Java
```java
class Solution {
    public int kthSmallest(int[][] mat, int k) {
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        maxHeap.offer(0);
        
        for (int[] row : mat) {
            PriorityQueue<Integer> nextMaxHeap = new PriorityQueue<>(Collections.reverseOrder());
            
            for (int sum : maxHeap) {
                for (int val : row) {
                    nextMaxHeap.offer(sum + val);
                    // Giữ lại nhiều nhất k tổng nhỏ nhất
                    if (nextMaxHeap.size() > k) {
                        nextMaxHeap.poll();
                    }
                }
            }
            maxHeap = nextMaxHeap;
        }
        
        return maxHeap.peek();
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(M * N * K * log K)` với `M` là số hàng, `N` là số cột.
- **Không gian (Space Complexity)**: `O(K)` cho bộ nhớ Heap.

## 29. Minimum Number of Days to Make m Bouquets (LeetCode 1482)
### Đề bài chi tiết
Bạn có một mảng `bloomDay`, nơi hoa thứ `i` nở vào ngày `bloomDay[i]`. Bạn cần tạo ra `m` bó hoa. Để làm 1 bó, bạn cần `k` bông hoa **liền kề** nhau. Tìm số ngày tối thiểu phải chờ để làm đủ `m` bó hoa (trả về `-1` nếu không thể).

### Phân tích thuật toán
- Bài này chủ yếu sử dụng Binary Search trên kết quả, kết hợp logic duyệt tuyến tính (Tham lam/Đánh dấu). Tuy không phải bài toán Heap cổ điển, nó thường xuất hiện ở chủ đề tìm trị cực đại, cực tiểu.
- Khoảng tìm kiếm: `low = 1`, `high = max(bloomDay)`.
- Ở mỗi mức `day`, duyệt qua mảng để đếm số lượng hoa liên tiếp đã nở. Nếu đủ `k` thì tạo thành 1 bó, nếu gặp hoa chưa nở thì reset bộ đếm liên tiếp.

### Mã nguồn Java
```java
class Solution {
    public int minDays(int[] bloomDay, int m, int k) {
        // Kiểm tra ngay xem có đủ số hoa tổng cộng không
        if ((long) m * k > bloomDay.length) return -1;
        
        int low = 1, high = 1000000000;
        while (low < high) {
            int mid = low + (high - low) / 2;
            if (canMake(bloomDay, m, k, mid)) {
                high = mid; // Có thể làm được, thử số ngày nhỏ hơn
            } else {
                low = mid + 1;
            }
        }
        
        return low;
    }
    
    private boolean canMake(int[] bloomDay, int m, int k, int day) {
        int bouquets = 0, consecutiveFlowers = 0;
        for (int bloom : bloomDay) {
            if (bloom <= day) {
                consecutiveFlowers++;
                if (consecutiveFlowers == k) {
                    bouquets++;
                    consecutiveFlowers = 0;
                }
            } else {
                consecutiveFlowers = 0; // Hoa chưa nở, ngắt chuỗi liền kề
            }
        }
        return bouquets >= m;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `O(N log(MaxDay))`.
- **Không gian (Space Complexity)**: `O(1)`.

## 30. Design Twitter (LeetCode 355)
### Đề bài chi tiết
Thiết kế Twitter cơ bản. Các chức năng:
- `postTweet(userId, tweetId)`: Người dùng đăng tweet.
- `getNewsFeed(userId)`: Lấy 10 bài tweet mới nhất trong news feed (bao gồm của chính user và những người họ follow).
- `follow(followerId, followeeId)`: Follow một user.
- `unfollow(followerId, followeeId)`: Unfollow một user.

### Phân tích thuật toán
- Mỗi `User` giữ danh sách các bài tweet của riêng họ. Danh sách này có thể được nối như một danh sách liên kết ngược (Linked List) để dễ lấy phần tử mới nhất.
- Khi cần lấy News Feed, ta thu thập tất cả "đỉnh" (tweet mới nhất) của bản thân và những người đang follow. Sử dụng k-way merge (dùng **Max-Heap** xếp theo thời gian) để hợp nhất và lấy ra top 10 bài đăng mới nhất.

### Mã nguồn Java
```java
class Twitter {
    private static int timeStamp = 0;
    private Map<Integer, User> userMap;

    private class Tweet {
        public int id;
        public int time;
        public Tweet next;
        
        public Tweet(int id) {
            this.id = id;
            time = timeStamp++;
            next = null;
        }
    }
    
    private class User {
        public int id;
        public Set<Integer> followed;
        public Tweet tweetHead;
        
        public User(int id) {
            this.id = id;
            followed = new HashSet<>();
            follow(id); // Luôn tự follow chính mình
            tweetHead = null;
        }
        
        public void follow(int id) {
            followed.add(id);
        }
        
        public void unfollow(int id) {
            if (id != this.id) {
                followed.remove(id);
            }
        }
        
        public void post(int id) {
            Tweet t = new Tweet(id);
            t.next = tweetHead;
            tweetHead = t;
        }
    }

    public Twitter() {
        userMap = new HashMap<>();
    }
    
    public void postTweet(int userId, int tweetId) {
        userMap.putIfAbsent(userId, new User(userId));
        userMap.get(userId).post(tweetId);
    }
    
    public List<Integer> getNewsFeed(int userId) {
        List<Integer> res = new ArrayList<>();
        if (!userMap.containsKey(userId)) return res;
        
        Set<Integer> users = userMap.get(userId).followed;
        PriorityQueue<Tweet> maxHeap = new PriorityQueue<>((a, b) -> b.time - a.time);
        
        for (int user : users) {
            Tweet t = userMap.get(user).tweetHead;
            if (t != null) {
                maxHeap.offer(t);
            }
        }
        
        int n = 0;
        while (!maxHeap.isEmpty() && n < 10) {
            Tweet t = maxHeap.poll();
            res.add(t.id);
            n++;
            if (t.next != null) {
                maxHeap.offer(t.next);
            }
        }
        
        return res;
    }
    
    public void follow(int followerId, int followeeId) {
        userMap.putIfAbsent(followerId, new User(followerId));
        userMap.putIfAbsent(followeeId, new User(followeeId));
        userMap.get(followerId).follow(followeeId);
    }
    
    public void unfollow(int followerId, int followeeId) {
        if (!userMap.containsKey(followerId) || followerId == followeeId) return;
        userMap.get(followerId).unfollow(followeeId);
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: `post`, `follow`, `unfollow` là `O(1)`. `getNewsFeed` là `O(F log F)` với `F` là số lượng người đang follow.
- **Không gian (Space Complexity)**: Tương đối với lượng dữ liệu người dùng và bài đăng, `O(U + T)`.
