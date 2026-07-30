# Bài 2: Interval Scheduling - Danh sách bài tập (Problems)

Tập hợp 30 bài toán về Interval Scheduling (Lập lịch khoảng thời gian) phân theo độ khó từ cơ bản đến nâng cao.

## 1. Merge Intervals (LeetCode 56)
**Đề bài chi tiết:** Cho một mảng `intervals` nơi `intervals[i] = [start_i, end_i]`, gộp tất cả các intervals bị chồng chéo (overlap) và trả về một mảng chứa các intervals rời rạc không chồng chéo bao phủ tất cả các intervals đầu vào.

**Phân tích thuật toán:**
- Sắp xếp các mảng theo thời gian bắt đầu (`start`).
- Tạo một danh sách kết quả, đặt phần tử đầu tiên vào.
- Duyệt qua các intervals còn lại, nếu thời gian bắt đầu của interval hiện tại $\le$ thời gian kết thúc của interval đang được gộp, ta cập nhật thời gian kết thúc bằng giá trị lớn nhất.
- Ngược lại, không chồng chéo, ta thêm interval mới vào kết quả.

**Mã nguồn Java:**
```java
class Solution {
    public int[][] merge(int[][] intervals) {
        if (intervals.length <= 1) return intervals;
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
        List<int[]> result = new ArrayList<>();
        int[] current = intervals[0];
        result.add(current);
        
        for (int i = 1; i < intervals.length; i++) {
            int[] next = intervals[i];
            if (next[0] <= current[1]) {
                current[1] = Math.max(current[1], next[1]);
            } else {
                current = next;
                result.add(current);
            }
        }
        return result.toArray(new int[result.size()][]);
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$ cho thao tác sort.
- Space: $O(N)$ cho danh sách kết quả.

---

## 2. Non-overlapping Intervals (LeetCode 435)
**Đề bài chi tiết:** Cho một mảng các khoảng `intervals`, tìm số lượng ít nhất các intervals cần xóa đi để các intervals còn lại không chồng chéo lên nhau.

**Phân tích thuật toán:**
- Đây là dạng bài toán Activity Selection.
- Ta muốn giữ lại **nhiều** intervals không chồng chéo nhất $\Rightarrow$ cần sắp xếp theo thời gian kết thúc (`end time`).
- Duyệt mảng: nếu interval hiện tại bắt đầu sớm hơn (hoặc bằng) `end` của interval trước đó, nó bị chồng chéo và ta đếm lên 1. Nếu không, cập nhật lại `end`.

**Mã nguồn Java:**
```java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        if (intervals.length == 0) return 0;
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[1], b[1]));
        
        int count = 0;
        int end = intervals[0][1];
        
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] < end) {
                count++;
            } else {
                end = intervals[i][1];
            }
        }
        return count;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$.
- Space: $O(1)$ hoặc $O(\log N)$ (tùy cài đặt sort).

---

## 3. Minimum Number of Arrows to Burst Balloons (LeetCode 452)
**Đề bài chi tiết:** Có một số bong bóng được biểu diễn bằng tọa độ `[x_start, x_end]`. Một mũi tên bắn tại tọa độ `x` có thể làm nổ bong bóng nếu `x_start <= x <= x_end`. Tìm số mũi tên ít nhất cần bắn để nổ hết bong bóng.

**Phân tích thuật toán:**
- Tương tự bài toán Activity Selection, tìm số tập giao nhau nhỏ nhất bao trùm tất cả.
- Sắp xếp mảng theo thời gian kết thúc (`end time`).
- Nếu một bong bóng bắt đầu sau khi quả mũi tên hiện tại được bắn (tức `start > current_end`), ta bắt buộc phải tốn thêm một mũi tên.

**Mã nguồn Java:**
```java
class Solution {
    public int findMinArrowShots(int[][] points) {
        if (points.length == 0) return 0;
        Arrays.sort(points, (a, b) -> Integer.compare(a[1], b[1]));
        
        int arrows = 1;
        int currentEnd = points[0][1];
        
        for (int i = 1; i < points.length; i++) {
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
- Time: $O(N \log N)$.
- Space: $O(1)$ hoặc $O(\log N)$.

---

## 4. Meeting Rooms (LeetCode 252)
**Đề bài chi tiết:** Cho một mảng khoảng thời gian các cuộc họp `intervals = [[start_i, end_i]]`. Xác định xem một người có thể tham gia toàn bộ các cuộc họp hay không (nghĩa là không có hai cuộc họp nào bị chồng chéo).

**Phân tích thuật toán:**
- Cần kiểm tra xem có 2 intervals nào overlap không.
- Sắp xếp theo `start time`. Nếu cuộc họp hiện tại bắt đầu trước khi cuộc họp trước kết thúc $\Rightarrow$ Overlap $\Rightarrow$ `return false`.

**Mã nguồn Java:**
```java
class Solution {
    public boolean canAttendMeetings(int[][] intervals) {
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] < intervals[i - 1][1]) {
                return false;
            }
        }
        return true;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$.
- Space: $O(1)$ hoặc $O(\log N)$.

---

## 5. Meeting Rooms II (LeetCode 253)
**Đề bài chi tiết:** Cho danh sách các cuộc họp `intervals`. Tìm số lượng phòng họp tối thiểu cần thiết để sắp xếp toàn bộ các cuộc họp này.

**Phân tích thuật toán:**
- Sử dụng Min-Heap (Priority Queue) để lưu thời gian kết thúc (`end time`) của các phòng đang sử dụng.
- Sắp xếp mảng theo `start time`.
- Duyệt qua từng cuộc họp: nếu phòng họp kết thúc sớm nhất (đỉnh Heap) $\le$ thời gian bắt đầu của cuộc họp mới, phòng đó có thể được tái sử dụng (ta `poll` ra).
- Luôn `add` cuộc họp hiện tại vào Heap. Kích thước Heap cuối cùng chính là số phòng tối thiểu.

**Mã nguồn Java:**
```java
class Solution {
    public int minMeetingRooms(int[][] intervals) {
        if (intervals.length == 0) return 0;
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        
        pq.add(intervals[0][1]);
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] >= pq.peek()) {
                pq.poll();
            }
            pq.add(intervals[i][1]);
        }
        return pq.size();
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$ (Sắp xếp + Thao tác Heap).
- Space: $O(N)$ cho Priority Queue.

---

## 6. Insert Interval (LeetCode 57)
**Đề bài chi tiết:** Cho mảng `intervals` đã được sắp xếp sẵn theo `start time` và không có overlapping, và một `newInterval`. Chèn `newInterval` vào mảng sao cho mảng vẫn sắp xếp và không bị overlap.

**Phân tích thuật toán:**
- Tạo list kết quả.
- B1: Duyệt và thêm thẳng các intervals kết thúc trước khi `newInterval` bắt đầu.
- B2: Khi gặp overlap (bắt đầu của interval hiện tại $\le$ kết thúc của `newInterval`), gộp chúng bằng cách lấy `start = min`, `end = max`. Lặp cho đến khi hết overlap. Thêm interval đã gộp vào.
- B3: Thêm phần còn lại của danh sách vào.

**Mã nguồn Java:**
```java
class Solution {
    public int[][] insert(int[][] intervals, int[] newInterval) {
        List<int[]> result = new ArrayList<>();
        int i = 0, n = intervals.length;
        
        while (i < n && intervals[i][1] < newInterval[0]) {
            result.add(intervals[i++]);
        }
        
        while (i < n && intervals[i][0] <= newInterval[1]) {
            newInterval[0] = Math.min(newInterval[0], intervals[i][0]);
            newInterval[1] = Math.max(newInterval[1], intervals[i][1]);
            i++;
        }
        result.add(newInterval);
        
        while (i < n) {
            result.add(intervals[i++]);
        }
        
        return result.toArray(new int[result.size()][]);
    }
}
```
**Độ phức tạp:**
- Time: $O(N)$ vì mảng đã được sort.
- Space: $O(N)$ chứa kết quả.

---

## 7. Video Stitching (LeetCode 1024)
**Đề bài chi tiết:** Bạn được cung cấp một mảng `clips` và `time`. Tìm số đoạn video tối thiểu cần ghép lại để bao phủ được khoảng `[0, time]`.

**Phân tích thuật toán:**
- Dạng Greedy (Jump Game style).
- Sắp xếp các đoạn theo `start time`, nếu trùng `start time` thì theo `end time` giảm dần.
- Dùng thuật toán tham lam tìm đoạn đi xa nhất có thể đạt được trong giới hạn hiện hành.

**Mã nguồn Java:**
```java
class Solution {
    public int videoStitching(int[][] clips, int time) {
        Arrays.sort(clips, (a, b) -> a[0] == b[0] ? b[1] - a[1] : a[0] - b[0]);
        int count = 0, currentEnd = 0, maxReach = 0;
        int i = 0, n = clips.length;
        
        while (currentEnd < time) {
            while (i < n && clips[i][0] <= currentEnd) {
                maxReach = Math.max(maxReach, clips[i][1]);
                i++;
            }
            if (currentEnd == maxReach) return -1; // Kẹt, không đi tiếp được
            currentEnd = maxReach;
            count++;
        }
        return count;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$ (Sắp xếp).
- Space: $O(1)$.

---

## 8. Interval List Intersections (LeetCode 986)
**Đề bài chi tiết:** Cho hai danh sách các intervals rời rạc và đã được sắp xếp. Tìm giao (intersection) của hai danh sách này.

**Phân tích thuật toán:**
- Sử dụng **2 Pointers (Hai con trỏ)** `i` và `j` duyệt qua hai mảng.
- Tìm điểm `start = max(A.start, B.start)` và `end = min(A.end, B.end)`.
- Nếu `start <= end`, đây là một khoảng giao nhau $\Rightarrow$ thêm vào kết quả.
- Tăng con trỏ của khoảng có `end` nhỏ hơn vì khoảng đó đã duyệt xong.

**Mã nguồn Java:**
```java
class Solution {
    public int[][] intervalIntersection(int[][] firstList, int[][] secondList) {
        List<int[]> ans = new ArrayList<>();
        int i = 0, j = 0;
        
        while (i < firstList.length && j < secondList.length) {
            int start = Math.max(firstList[i][0], secondList[j][0]);
            int end = Math.min(firstList[i][1], secondList[j][1]);
            
            if (start <= end) {
                ans.add(new int[]{start, end});
            }
            
            if (firstList[i][1] < secondList[j][1]) {
                i++;
            } else {
                j++;
            }
        }
        return ans.toArray(new int[ans.size()][]);
    }
}
```
**Độ phức tạp:**
- Time: $O(N + M)$ (với N, M là độ dài 2 mảng).
- Space: $O(N + M)$ cho mảng kết quả.

---

## 9. Employee Free Time (LeetCode 759)
**Đề bài chi tiết:** Có một danh sách thời gian làm việc của các nhân viên. Tìm khoảng thời gian rảnh chung của tất cả nhân viên.

**Phân tích thuật toán:**
- Bài toán gộp toàn bộ lịch của mọi nhân viên lại (có thể gộp vào 1 List 1D), sau đó sắp xếp theo `start time`.
- Duyệt tương tự bài Merge Intervals: gộp các khoảng thời gian lại, duy trì một `max_end`.
- Bất cứ khi nào khoảng tiếp theo có `start > max_end`, ta tìm được một free time `[max_end, start]`.

**Mã nguồn Java:**
```java
/*
// Definition for an Interval.
class Interval {
    public int start;
    public int end;
    public Interval() {}
    public Interval(int _start, int _end) { start = _start; end = _end; }
};
*/
class Solution {
    public List<Interval> employeeFreeTime(List<List<Interval>> schedule) {
        List<Interval> all = new ArrayList<>();
        for (List<Interval> emp : schedule) {
            all.addAll(emp);
        }
        all.sort((a, b) -> Integer.compare(a.start, b.start));
        
        List<Interval> res = new ArrayList<>();
        int maxEnd = all.get(0).end;
        
        for (int i = 1; i < all.size(); i++) {
            Interval curr = all.get(i);
            if (curr.start > maxEnd) {
                res.add(new Interval(maxEnd, curr.start));
            }
            maxEnd = Math.max(maxEnd, curr.end);
        }
        return res;
    }
}
```
**Độ phức tạp:**
- Time: $O(K \log K)$ với $K$ là tổng số lượng khoảng làm việc.
- Space: $O(K)$ để lưu danh sách gộp.

---

## 10. Remove Covered Intervals (LeetCode 1288)
**Đề bài chi tiết:** Cho một mảng `intervals`. Xóa các intervals bị bao trùm hoàn toàn bởi một interval khác (`[c, d]` bị bao trùm bởi `[a, b]` nếu `a <= c` và `d <= b`). Tìm số intervals còn lại.

**Phân tích thuật toán:**
- Sắp xếp theo `start time` tăng dần. NẾU `start time` bằng nhau, ta cần sắp xếp `end time` GIẢM DẦN. (Tại sao? Để interval lớn hơn xuất hiện trước, bao trùm interval bé hơn).
- Duyệt qua mảng, duy trì một biến `max_end`. Nếu `interval.end <= max_end`, nó đã bị bao trùm, tiến hành đếm số bị xóa. Nếu lớn hơn, nó không bị bao trùm, cập nhật lại `max_end`.

**Mã nguồn Java:**
```java
class Solution {
    public int removeCoveredIntervals(int[][] intervals) {
        Arrays.sort(intervals, (a, b) -> a[0] == b[0] ? b[1] - a[1] : a[0] - b[0]);
        int count = 0, maxEnd = 0;
        
        for (int[] interval : intervals) {
            if (interval[1] <= maxEnd) {
                count++;
            } else {
                maxEnd = interval[1];
            }
        }
        return intervals.length - count;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$.
- Space: $O(1)$ hoặc $O(\log N)$.

---

## 11. Data Stream as Disjoint Intervals (LeetCode 352)
**Đề bài chi tiết:** Cho một luồng dữ liệu (data stream) các số nguyên không âm, tóm tắt các số đã thấy dưới dạng một danh sách các khoảng thời gian (intervals) rời rạc. Triển khai lớp `SummaryRanges` với các phương thức `addNum(value)` để thêm số vào luồng và `getIntervals()` trả về danh sách các khoảng.

**Phân tích thuật toán:**
- Sử dụng `TreeMap` trong Java để duy trì cấu trúc các khoảng thời gian với `key` là `start`, `value` là `end`.
- Khi thêm một số `val`, ta tìm kiếm các khoảng kề trái và kề phải của `val` bằng `lowerKey` và `higherKey` (hoặc `floorKey`).
- Sau đó tiến hành gộp (merge) nếu `val` liền kề nối khoảng trái và phải lại, hoặc mở rộng khoảng trái, hoặc mở rộng khoảng phải. Nếu không thì tạo một khoảng mới.

**Mã nguồn Java:**
```java
class SummaryRanges {
    private TreeMap<Integer, Integer> map;

    public SummaryRanges() {
        map = new TreeMap<>();
    }
    
    public void addNum(int val) {
        if (map.containsKey(val)) return;
        
        Integer lowerKey = map.lowerKey(val);
        Integer higherKey = map.higherKey(val);
        
        boolean mergeLeft = lowerKey != null && map.get(lowerKey) >= val - 1;
        boolean mergeRight = higherKey != null && higherKey == val + 1;
        
        if (mergeLeft && mergeRight) {
            map.put(lowerKey, map.get(higherKey));
            map.remove(higherKey);
        } else if (mergeLeft) {
            map.put(lowerKey, Math.max(map.get(lowerKey), val));
        } else if (mergeRight) {
            map.put(val, map.get(higherKey));
            map.remove(higherKey);
        } else {
            map.put(val, val);
        }
    }
    
    public int[][] getIntervals() {
        int[][] res = new int[map.size()][2];
        int i = 0;
        for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
            res[i++] = new int[]{entry.getKey(), entry.getValue()};
        }
        return res;
    }
}
```
**Độ phức tạp:**
- Time: $O(\log N)$ cho thao tác `addNum`, $O(N)$ cho `getIntervals`.
- Space: $O(N)$ lưu trữ số lượng intervals.

---

## 12. Range Module (LeetCode 715)
**Đề bài chi tiết:** Thiết kế một cấu trúc dữ liệu theo dõi các nửa khoảng (half-open intervals) và thực hiện các truy vấn trên chúng. Cần hỗ trợ: `addRange(left, right)` thêm nửa khoảng `[left, right)`, `queryRange(left, right)` trả về `true` nếu mọi số thực thuộc `[left, right)` đều được theo dõi, `removeRange(left, right)` dừng theo dõi các số thuộc đoạn này.

**Phân tích thuật toán:**
- Sử dụng `TreeMap` với `key` = `start`, `value` = `end`.
- Với `addRange`, ta gộp các khoảng bị đè hoặc chạm nhau với `[left, right]`. Ta tìm các điểm `floorKey` và cập nhật lại ranh giới bao trùm, sau đó xóa các khoảng kẹp giữa.
- Với `removeRange`, nếu khoảng xóa cắt ngang một khoảng hiện có, ta tách khoảng cũ ra và xóa đi đoạn giao nhau. Việc duy trì tính rời rạc ở `TreeMap` thông qua `subMap` cho phép ta dọn sạch các elements bên trong nhanh chóng.

**Mã nguồn Java:**
```java
class RangeModule {
    private TreeMap<Integer, Integer> map;

    public RangeModule() {
        map = new TreeMap<>();
    }
    
    public void addRange(int left, int right) {
        Integer start = map.floorKey(left);
        Integer end = map.floorKey(right);
        
        if (start != null && map.get(start) >= left) {
            left = start;
        }
        if (end != null && map.get(end) > right) {
            right = map.get(end);
        }
        
        map.subMap(left, right).clear();
        map.put(left, right);
    }
    
    public boolean queryRange(int left, int right) {
        Integer start = map.floorKey(left);
        return start != null && map.get(start) >= right;
    }
    
    public void removeRange(int left, int right) {
        Integer start = map.floorKey(left);
        Integer end = map.floorKey(right);
        
        if (end != null && map.get(end) > right) {
            map.put(right, map.get(end));
        }
        if (start != null && map.get(start) > left) {
            map.put(start, left);
        }
        
        map.subMap(left, right).clear();
    }
}
```
**Độ phức tạp:**
- Time: $O(K \log N)$ (khấu hao về $O(\log N)$) cho `addRange` và `removeRange` (K là lượng interval bị gộp/xóa). $O(\log N)$ cho `queryRange`.
- Space: $O(N)$ lưu trữ danh sách khoảng.

---

## 13. My Calendar I (LeetCode 729)
**Đề bài chi tiết:** Bạn cần triển khai một chương trình quản lý sự kiện (`MyCalendar`). Một sự kiện mới có thể được thêm nếu nó không bị chồng chéo (overlap) với bất kỳ sự kiện nào khác. Hàm `book(start, end)` trả về `true` nếu sự kiện được thêm thành công.

**Phân tích thuật toán:**
- Lưu các đoạn `[start, end)`. Ta dùng `TreeMap` trong Java.
- Khi gọi lệnh `book`, ta tìm khoảng liền trước bằng `floorKey` và khoảng liền sau bằng `ceilingKey`.
- Nếu khoảng liền trước kết thúc trước hoặc đúng `start`, và khoảng liền sau bắt đầu muộn hơn hoặc bằng `end` thì không có sự chồng chéo.
- Ta chèn vào `TreeMap` nếu đủ điều kiện và trả về `true`. Ngược lại `false`.

**Mã nguồn Java:**
```java
class MyCalendar {
    private TreeMap<Integer, Integer> map;

    public MyCalendar() {
        map = new TreeMap<>();
    }
    
    public boolean book(int start, int end) {
        Integer prev = map.floorKey(start);
        Integer next = map.ceilingKey(start);
        
        if ((prev == null || map.get(prev) <= start) && 
            (next == null || next >= end)) {
            map.put(start, end);
            return true;
        }
        return false;
    }
}
```
**Độ phức tạp:**
- Time: $O(\log N)$ mỗi truy vấn `book`.
- Space: $O(N)$ để lưu trữ.

---

## 14. My Calendar II (LeetCode 731)
**Đề bài chi tiết:** Cài đặt lớp `MyCalendarTwo` quản lý sự kiện có thể cho phép **tối đa 2** sự kiện trùng nhau tại cùng một thời điểm. Nếu thêm một sự kiện tạo thành 3 sự kiện bị trùng cùng một lúc, từ chối và trả về `false`.

**Phân tích thuật toán:**
- Dùng kỹ thuật Sweep Line (Quét đường). Ta dùng một `TreeMap` đếm tần suất, mốc `start` thì cộng `+1`, mốc `end` trừ `-1`.
- Với mỗi lần `book`, ta ghi nhận thay đổi thời gian. Sau đó duyệt trên tập keys, tính tổng cộng dồn số lượng sự kiện `active`.
- Nếu `active >= 3` ở bất kỳ thời điểm nào, sự kiện này bị vi phạm, ta lập tức phục hồi lại `TreeMap` (xóa thay đổi) và trả về `false`.

**Mã nguồn Java:**
```java
class MyCalendarTwo {
    private TreeMap<Integer, Integer> delta;

    public MyCalendarTwo() {
        delta = new TreeMap<>();
    }
    
    public boolean book(int start, int end) {
        delta.put(start, delta.getOrDefault(start, 0) + 1);
        delta.put(end, delta.getOrDefault(end, 0) - 1);
        
        int active = 0;
        for (int count : delta.values()) {
            active += count;
            if (active >= 3) {
                // Phục hồi lại
                delta.put(start, delta.get(start) - 1);
                delta.put(end, delta.get(end) + 1);
                if (delta.get(start) == 0) delta.remove(start);
                if (delta.get(end) == 0) delta.remove(end);
                return false;
            }
        }
        return true;
    }
}
```
**Độ phức tạp:**
- Time: $O(N)$ mỗi truy vấn `book` do phải duyệt Map.
- Space: $O(N)$ lưu các mốc thời gian.

---

## 15. My Calendar III (LeetCode 732)
**Đề bài chi tiết:** Triển khai `MyCalendarThree`, mỗi lần gọi hàm `book(start, end)`, hãy trả về số lượng lớn nhất các sự kiện bị chồng chéo nhau tại cùng một thời điểm ở lịch hiện tại (K-booking lớn nhất).

**Phân tích thuật toán:**
- Tương tự bài My Calendar II, sử dụng `TreeMap` cho Sweep Line.
- `start` đánh dấu `+1`, `end` đánh dấu `-1`.
- Mỗi lần `book`, ta cập nhật map rồi duyệt tuần tự từ đầu đến cuối map, liên tục tính tổng số sự kiện đang diễn ra bằng biến cộng dồn `active`.
- Trả về giá trị `maxOverlap` (giá trị lớn nhất của `active` đo được).

**Mã nguồn Java:**
```java
class MyCalendarThree {
    private TreeMap<Integer, Integer> delta;

    public MyCalendarThree() {
        delta = new TreeMap<>();
    }
    
    public int book(int start, int end) {
        delta.put(start, delta.getOrDefault(start, 0) + 1);
        delta.put(end, delta.getOrDefault(end, 0) - 1);
        
        int active = 0;
        int maxOverlap = 0;
        for (int count : delta.values()) {
            active += count;
            maxOverlap = Math.max(maxOverlap, active);
        }
        
        return maxOverlap;
    }
}
```
**Độ phức tạp:**
- Time: $O(N)$ cho mỗi lần gọi `book`.
- Space: $O(N)$.

---

## 16. Find Right Interval (LeetCode 436)
**Đề bài chi tiết:** Cho một mảng `intervals`, trả về mảng chứa chỉ số của "Right Interval" cho mỗi khoảng. "Right Interval" của khoảng $i$ là khoảng $j$ sao cho $start_j \ge end_i$, và $start_j$ là bé nhất. Nếu không có trả về -1.

**Phân tích thuật toán:**
- Sử dụng `TreeMap` để ánh xạ từ `start time` sang chỉ số (index) gốc của interval.
- Duyệt qua từng interval trong mảng ban đầu, với `end` của khoảng hiện tại, ta gọi `ceilingEntry(end)` để tìm mục trong `TreeMap` có `start` $\ge end$ nhưng nhỏ nhất có thể.
- Ghi nhận `index` vào mảng kết quả.

**Mã nguồn Java:**
```java
class Solution {
    public int[] findRightInterval(int[][] intervals) {
        int[] result = new int[intervals.length];
        TreeMap<Integer, Integer> map = new TreeMap<>();
        
        for (int i = 0; i < intervals.length; i++) {
            map.put(intervals[i][0], i);
        }
        
        for (int i = 0; i < intervals.length; i++) {
            Map.Entry<Integer, Integer> entry = map.ceilingEntry(intervals[i][1]);
            result[i] = (entry != null) ? entry.getValue() : -1;
        }
        
        return result;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$ để xây dựng TreeMap và tra cứu.
- Space: $O(N)$ cho không gian `TreeMap` và mảng kết quả.

---

## 17. Maximum Length of Pair Chain (LeetCode 646)
**Đề bài chi tiết:** Cho mảng `pairs` với `pairs[i] = [left, right]`. Cặp `[c, d]` có thể nối tiếp sau `[a, b]` nếu `b < c`. Trả về độ dài lớn nhất của chuỗi có thể tạo thành.

**Phân tích thuật toán:**
- Bản chất giống bài Non-overlapping Intervals (Activity Selection problem). Ta muốn xếp nhiều cặp nhất có thể mà không bị gối lên nhau.
- Sắp xếp các đoạn dựa trên `end time` tăng dần.
- Khởi tạo điểm chốt `currEnd = Integer.MIN_VALUE`. Lặp qua từng đoạn, nếu đoạn hiện tại bắt đầu trễ hơn `currEnd` (`start > currEnd`), ta lấy đoạn đó và tăng độ dài chuỗi lên 1, đồng thời gán `currEnd` bằng `end` của đoạn này.

**Mã nguồn Java:**
```java
class Solution {
    public int findLongestChain(int[][] pairs) {
        Arrays.sort(pairs, (a, b) -> Integer.compare(a[1], b[1]));
        
        int currEnd = Integer.MIN_VALUE;
        int count = 0;
        
        for (int[] pair : pairs) {
            if (pair[0] > currEnd) {
                count++;
                currEnd = pair[1];
            }
        }
        
        return count;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$ do sắp xếp.
- Space: $O(1)$ hoặc $O(\log N)$ tùy thuật toán sort của Java.

---

## 18. Teemo Attacking (LeetCode 495)
**Đề bài chi tiết:** Teemo đánh vào các thời điểm trong `timeSeries`, độc kéo dài `duration` giây. Nếu đánh tiếp khi mục tiêu đang dính độc, thời gian độc bị làm mới. Tính tổng thời gian mục tiêu bị nhiễm độc.

**Phân tích thuật toán:**
- Duyệt qua từng nhát đánh. Với nhát đánh thứ $i$, khoảng thời gian kéo dài bình thường là `duration`. Tuy nhiên, nếu thời điểm nhát đánh tiếp theo $t_{i+1}$ xuất hiện sớm hơn thời gian hết độc, tổng độc bị giới hạn lại đúng bằng khoảng cách $t_{i+1} - t_i$.
- Vì thế tại bước $i$, ta cộng thêm lượng $\min(timeSeries[i+1] - timeSeries[i], duration)$.
- Tại nhát đánh cuối cùng, chắc chắn độc tồn tại đủ trọn vẹn `duration` giây.

**Mã nguồn Java:**
```java
class Solution {
    public int findPoisonedDuration(int[] timeSeries, int duration) {
        if (timeSeries.length == 0) return 0;
        int total = 0;
        
        for (int i = 0; i < timeSeries.length - 1; i++) {
            total += Math.min(timeSeries[i + 1] - timeSeries[i], duration);
        }
        
        // Cộng thời gian đầy đủ cho nhát đánh cuối cùng
        total += duration;
        return total;
    }
}
```
**Độ phức tạp:**
- Time: $O(N)$ với $N$ là số lượng nhát đánh.
- Space: $O(1)$.

---

## 19. Task Scheduler (LeetCode 621)
**Đề bài chi tiết:** Xếp lịch một tập hợp các công việc `tasks` (biểu diễn bằng chữ in hoa). Cùng một loại công việc phải cách nhau ít nhất `n` đơn vị thời gian. Tìm khoảng thời gian tối thiểu (số chu kỳ bao gồm cả rảnh rỗi - idle) để hoàn thành tất cả.

**Phân tích thuật toán:**
- Tìm tần suất xuất hiện nhiều nhất của một công việc (`maxFreq`), vì công việc này sẽ chi phối toàn bộ lịch.
- Sẽ có ít nhất `(maxFreq - 1)` khoảng trống lớn. Mỗi khoảng trống cần tối thiểu `n` chu kỳ, vậy kích thước phần có khoảng trống là `(maxFreq - 1) * (n + 1)`.
- Nếu có nhiều loại công việc cùng chia sẻ tần suất cao nhất `maxFreq`, ta cộng thêm lượng này vào (`maxCount`).
- Nếu mảng rất nhiều task nhỏ khác, khoảng cách thực tế sẽ lấp đầy hết `idle`, độ dài tối thiểu phải là số lượng `tasks` ban đầu. Kết quả là hàm lớn nhất (`max`) của 2 con số này.

**Mã nguồn Java:**
```java
class Solution {
    public int leastInterval(char[] tasks, int n) {
        int[] counts = new int[26];
        for (char task : tasks) {
            counts[task - 'A']++;
        }
        
        Arrays.sort(counts);
        int maxFreq = counts[25];
        
        // Đếm số lượng task có cùng maxFreq
        int maxCount = 0;
        for (int i = 25; i >= 0; i--) {
            if (counts[i] == maxFreq) maxCount++;
            else break;
        }
        
        int result = (maxFreq - 1) * (n + 1) + maxCount;
        return Math.max(result, tasks.length);
    }
}
```
**Độ phức tạp:**
- Time: $O(N)$ duyệt qua số lượng task, các mảng độ dài cố định tính là $O(1)$.
- Space: $O(1)$ (mảng cố định 26 chữ cái).

---

## 20. Car Pooling (LeetCode 1094)
**Đề bài chi tiết:** Một xe khách có sức chứa `capacity`. Bạn có `trips[i] = [num_passengers, start_location, end_location]`. Có thể chở tất cả mọi người theo danh sách hay không mà không vượt quá `capacity`?

**Phân tích thuật toán:**
- Kỹ thuật Line Sweeping rất phù hợp do giới hạn vị trí nhỏ (đến $1000$).
- Dùng một mảng `timestamp` gồm 1001 phần tử để lưu sự biến thiên: khách lên ở `start` thì `timestamp[start] += num`, khách xuống ở `end` thì `timestamp[end] -= num`.
- Duyệt dọc theo trạm xe từ $0 \dots 1000$, cộng dồn lượng khách vào `currentCapacity`. Nếu có trạm vượt quá `capacity`, trả về `false`.

**Mã nguồn Java:**
```java
class Solution {
    public boolean carPooling(int[][] trips, int capacity) {
        int[] timestamp = new int[1001];
        
        for (int[] trip : trips) {
            timestamp[trip[1]] += trip[0]; // Khách lên
            timestamp[trip[2]] -= trip[0]; // Khách xuống
        }
        
        int currentCapacity = 0;
        for (int pass : timestamp) {
            currentCapacity += pass;
            if (currentCapacity > capacity) {
                return false;
            }
        }
        
        return true;
    }
}
```
**Độ phức tạp:**
- Time: $O(N + 1000)$ tương đương $O(N)$.
- Space: $O(1)$ (cấp phát tĩnh mảng 1001).

---

## 21. Corporate Flight Bookings (LeetCode 1109)
**Đề bài chi tiết:** Có $n$ chuyến bay và mảng `bookings`, trong đó `bookings[i] = [first, last, seats]` biểu thị một người đặt `seats` ghế cho mỗi chuyến bay trong khoảng từ `first` đến `last` (bao gồm cả hai). Trả về một mảng chứa tổng số ghế được đặt cho mỗi chuyến bay từ 1 đến $n$.

**Phân tích thuật toán:**
- Bài toán này là một ví dụ kinh điển của kỹ thuật mảng hiệu (Difference Array) hay Sweep Line. 
- Thay vì cộng `seats` cho từng chuyến bay từ `first` đến `last` tốn $O(N)$ cho mỗi thao tác, ta chỉ cần:
  - Tại chuyến bay `first`, cộng thêm `seats`: `res[first - 1] += seats`.
  - Tại chuyến bay `last + 1` (ngay sau chuyến bay cuối), trừ đi `seats`: `res[last] -= seats`.
- Sau đó, duyệt một lần để cộng dồn mảng, ta sẽ thu được mảng kết quả cuối cùng.

**Mã nguồn Java:**
```java
class Solution {
    public int[] corpFlightBookings(int[][] bookings, int n) {
        int[] res = new int[n];
        for (int[] b : bookings) {
            res[b[0] - 1] += b[2];
            if (b[1] < n) {
                res[b[1]] -= b[2];
            }
        }
        for (int i = 1; i < n; i++) {
            res[i] += res[i - 1];
        }
        return res;
    }
}
```
**Độ phức tạp:**
- Time: $O(N + K)$ với $N$ là số chuyến bay và $K$ là số lượng booking.
- Space: $O(1)$ không tính mảng kết quả trả về.

---

## 22. Divide Intervals Into Minimum Number of Groups (LeetCode 2406)
**Đề bài chi tiết:** Cho một mảng 2D `intervals`, chia các khoảng thành số lượng nhóm tối thiểu sao cho không có khoảng nào trong cùng một nhóm giao nhau (overlap).

**Phân tích thuật toán:**
- Bài toán này hoàn toàn tương đương với Meeting Rooms II. Ta cần tìm số lượng khoảng giao nhau lớn nhất tại bất kỳ thời điểm nào.
- Dùng kỹ thuật Sweep Line với 2 mảng `start` và `end` tách biệt sẽ nhanh hơn.
- Tách riêng `start` và `end` thành hai mảng và sắp xếp độc lập.
- Dùng 2 con trỏ duyệt mảng. Nếu `start[i] <= end[endIndex]`, tức là bị giao, cần thêm 1 nhóm. Ngược lại, nhóm cũ đã kết thúc, ta tái sử dụng nhóm đó (`endIndex++`).

**Mã nguồn Java:**
```java
class Solution {
    public int minGroups(int[][] intervals) {
        int n = intervals.length;
        int[] start = new int[n];
        int[] end = new int[n];
        for (int i = 0; i < n; i++) {
            start[i] = intervals[i][0];
            end[i] = intervals[i][1];
        }
        Arrays.sort(start);
        Arrays.sort(end);
        
        int groups = 0;
        int endIndex = 0;
        for (int i = 0; i < n; i++) {
            if (start[i] <= end[endIndex]) {
                groups++;
            } else {
                endIndex++;
            }
        }
        return groups;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$ cho việc sắp xếp 2 mảng.
- Space: $O(N)$ để lưu trữ `start` và `end`.

---

## 23. Determine if Two Events Have Conflict (LeetCode 2446)
**Đề bài chi tiết:** Cho 2 sự kiện dạng mảng chuỗi thời gian `event1 = ["HH:MM", "HH:MM"]` và `event2 = ["HH:MM", "HH:MM"]`. Kiểm tra xem hai sự kiện này có xung đột (giao nhau) hay không.

**Phân tích thuật toán:**
- Do định dạng `"HH:MM"` là chuẩn 24 giờ và có độ dài cố định, ta hoàn toàn có thể so sánh trực tiếp bằng thứ tự từ điển (Lexicographical order) của String mà không cần chuyển sang số phút.
- Hai sự kiện giao nhau khi và chỉ khi điểm kết thúc của sự kiện này lớn hơn hoặc bằng điểm bắt đầu của sự kiện kia (và ngược lại).
- Công thức: `event1[0] <= event2[1] && event2[0] <= event1[1]`.

**Mã nguồn Java:**
```java
class Solution {
    public boolean haveConflict(String[] event1, String[] event2) {
        return event1[0].compareTo(event2[1]) <= 0 && event2[0].compareTo(event1[1]) <= 0;
    }
}
```
**Độ phức tạp:**
- Time: $O(1)$ do so sánh chuỗi độ dài cố định là hằng số.
- Space: $O(1)$.

---

## 24. Minimum Number of Taps to Open to Water a Garden (LeetCode 1326)
**Đề bài chi tiết:** Có một khu vườn từ $0$ đến $n$ và mảng `ranges`. Vòi nước thứ $i$ tưới được khoảng `[i - ranges[i], i + ranges[i]]`. Tìm số vòi nước tối thiểu cần mở để tưới toàn bộ vườn `[0, n]`.

**Phân tích thuật toán:**
- Đây là bài toán biến thể của Jump Game II / Video Stitching.
- Từ mỗi điểm $i$, ta tính toán được giới hạn trái và phải. Ta cần xây dựng một mảng `maxReach` với `maxReach[left] = max(right)`.
- Dùng thuật toán tham lam: duyệt từ $0$ đến $n$, theo dõi `currentEnd` (giới hạn tưới của vòi hiện tại) và `nextEnd` (giới hạn xa nhất ta có thể với tới nếu mở thêm một vòi nữa trong phạm vi `currentEnd`).
- Nếu đến giới hạn `currentEnd` mà chưa tưới hết, ta mở vòi tiếp theo (tăng số vòi) và cập nhật `currentEnd = nextEnd`.

**Mã nguồn Java:**
```java
class Solution {
    public int minTaps(int n, int[] ranges) {
        int[] maxReach = new int[n + 1];
        for (int i = 0; i <= n; i++) {
            if (ranges[i] == 0) continue;
            int left = Math.max(0, i - ranges[i]);
            int right = Math.min(n, i + ranges[i]);
            maxReach[left] = Math.max(maxReach[left], right);
        }
        
        int taps = 0, currEnd = 0, nextEnd = 0;
        for (int i = 0; i < n; i++) {
            nextEnd = Math.max(nextEnd, maxReach[i]);
            if (i == currEnd) {
                if (nextEnd <= i) return -1;
                taps++;
                currEnd = nextEnd;
            }
        }
        return taps;
    }
}
```
**Độ phức tạp:**
- Time: $O(N)$ vì chỉ cần duyệt qua mảng kích thước $N$.
- Space: $O(N)$ lưu mảng `maxReach`.

---

## 25. Minimum Intervals to Include Each Query (LeetCode 1851)
**Đề bài chi tiết:** Cho mảng `intervals` và một mảng `queries`. Truy vấn `queries[i]` là một số `q`, cần tìm một interval `[left, right]` chứa `q` sao cho độ dài `(right - left + 1)` là nhỏ nhất. Trả về mảng kết quả, nếu không tìm thấy trả về -1.

**Phân tích thuật toán:**
- Bài toán này có thể giải quyết bằng phương pháp Truy vấn Offline (Offline Queries). Thay vì xử lý từng query từ đầu, ta:
- Sắp xếp mảng `intervals` theo `start`. Sắp xếp mảng `queries` tăng dần (nhưng phải lưu lại index gốc để điền kết quả).
- Dùng một Priority Queue (Min-Heap) lưu các intervals đang chứa `q`. Heap này sắp xếp theo độ dài interval tăng dần.
- Với mỗi query, đưa tất cả các intervals có `start <= q` vào Heap. Sau đó, loại bỏ các intervals ở đỉnh Heap có `end < q` (vì nó không còn chứa `q`). Interval ở đỉnh Heap lúc này là khoảng ngắn nhất chứa `q`.

**Mã nguồn Java:**
```java
class Solution {
    public int[] minInterval(int[][] intervals, int[] queries) {
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
        int[][] sortedQueries = new int[queries.length][2];
        for (int i = 0; i < queries.length; i++) {
            sortedQueries[i] = new int[]{queries[i], i};
        }
        Arrays.sort(sortedQueries, (a, b) -> Integer.compare(a[0], b[0]));
        
        int[] res = new int[queries.length];
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> 
            Integer.compare(a[1] - a[0], b[1] - b[0])
        );
        
        int i = 0;
        for (int[] q : sortedQueries) {
            int queryVal = q[0];
            int queryIndex = q[1];
            
            while (i < intervals.length && intervals[i][0] <= queryVal) {
                pq.add(intervals[i]);
                i++;
            }
            while (!pq.isEmpty() && pq.peek()[1] < queryVal) {
                pq.poll();
            }
            
            res[queryIndex] = pq.isEmpty() ? -1 : (pq.peek()[1] - pq.peek()[0] + 1);
        }
        return res;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N + Q \log Q)$ để sắp xếp intervals và queries. Với Heap, mỗi interval ra vào 1 lần tốn tổng cộng $O(N \log N)$.
- Space: $O(N + Q)$ lưu trữ Heap và dữ liệu queries sao chép.

---

## 26. Describe the Painting (LeetCode 1943)
**Đề bài chi tiết:** Cho danh sách các mảng `segments` biểu thị việc tô màu đoạn thẳng `[start, end]` với mã màu `color`. Các màu cộng dồn tạo thành một màu tổng hợp. Trả về cấu hình bức tranh biểu thị dưới dạng danh sách các mảng `[start, end, sum_color]`.

**Phân tích thuật toán:**
- Dùng kỹ thuật Line Sweep kết hợp với `TreeMap`. Ta tính sự thay đổi màu: cộng màu ở mốc `start` và trừ màu ở mốc `end`.
- `TreeMap` giúp lưu trữ tự động tăng dần các điểm ngắt. Bất kỳ key nào trong `TreeMap` cũng là ranh giới của một đoạn màu mới.
- Duyệt qua `TreeMap`, giữ tổng màu tích lũy `currentSum`. Nếu `currentSum > 0`, khoảng `[lastPoint, currentPoint]` được tô màu với mã tổng hợp `currentSum`.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<Long>> splitPainting(int[][] segments) {
        TreeMap<Integer, Long> map = new TreeMap<>();
        for (int[] seg : segments) {
            map.put(seg[0], map.getOrDefault(seg[0], 0L) + seg[2]);
            map.put(seg[1], map.getOrDefault(seg[1], 0L) - seg[2]);
        }
        
        List<List<Long>> res = new ArrayList<>();
        long currentSum = 0;
        int lastPoint = -1;
        
        for (Map.Entry<Integer, Long> entry : map.entrySet()) {
            int currentPoint = entry.getKey();
            if (currentSum > 0 && lastPoint != -1) {
                res.add(Arrays.asList((long) lastPoint, (long) currentPoint, currentSum));
            }
            currentSum += entry.getValue();
            lastPoint = currentPoint;
        }
        return res;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$ để xây dựng cấu trúc `TreeMap`.
- Space: $O(N)$ lưu trữ các điểm thay đổi.

---

## 27. Count Days Spent Together (LeetCode 2409)
**Đề bài chi tiết:** Cho chuỗi định dạng `"MM-DD"` mô tả ngày đến và ngày đi của 2 người, Alice và Bob. Tìm số ngày họ cùng có mặt ở thành phố (khoảng thời gian giao nhau).

**Phân tích thuật toán:**
- Chuyển đổi ngày định dạng `"MM-DD"` ra số ngày trôi qua kể từ đầu năm (từ $1$ đến $365$).
- Bài toán trở thành tìm khoảng giao (intersection) của hai khoảng thời gian `[startA, endA]` và `[startB, endB]`.
- Vùng giao sẽ bắt đầu từ $\max(startA, startB)$ và kết thúc ở $\min(endA, endB)$. Nếu vùng giao hợp lệ, số ngày là `end - start + 1`.

**Mã nguồn Java:**
```java
class Solution {
    private int[] daysInMonth = {0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
    
    public int countDaysTogether(String arriveAlice, String leaveAlice, String arriveBob, String leaveBob) {
        int aStart = toDays(arriveAlice);
        int aEnd = toDays(leaveAlice);
        int bStart = toDays(arriveBob);
        int bEnd = toDays(leaveBob);
        
        int start = Math.max(aStart, bStart);
        int end = Math.min(aEnd, bEnd);
        
        return Math.max(0, end - start + 1);
    }
    
    private int toDays(String date) {
        int month = Integer.parseInt(date.substring(0, 2));
        int day = Integer.parseInt(date.substring(3, 5));
        int total = 0;
        for (int i = 1; i < month; i++) {
            total += daysInMonth[i];
        }
        return total + day;
    }
}
```
**Độ phức tạp:**
- Time: $O(1)$. Tính toán hằng số trong giới hạn 12 tháng.
- Space: $O(1)$.

---

## 28. Amount of New Area Painted Each Day (LeetCode 2158)
**Đề bài chi tiết:** Cho mảng `paint` với `paint[i] = [start, end]` biểu thị đoạn cần sơn ở ngày thứ $i$. Tính lượng diện tích **mới** được sơn ở ngày $i$ (chỉ tính diện tích chưa từng được sơn trước đó).

**Phân tích thuật toán:**
- Dùng kỹ thuật Disjoint Set Union (DSU) hoặc Jump Pointer để đánh dấu các ô đã sơn và nhảy qua nhanh chóng.
- Khởi tạo mảng `jump` với mặc định là $0$. Khi sơn đoạn `[start, end]`, ta duyệt bằng con trỏ `start`.
- Nếu ô `start` chưa sơn, ta tăng đếm số lượng mới, gán `jump[start] = end` (nhảy xa nhất có thể) và tiến con trỏ lên 1.
- Nếu ô `start` đã được sơn, ta lập tức lấy vị trí nhảy tiếp theo `nextStart = jump[start]`, đồng thời cập nhật trỏ thẳng `jump[start]` bằng max của nó và `end` để tối ưu (path compression).

**Mã nguồn Java:**
```java
class Solution {
    public int[] amountPainted(int[][] paint) {
        int[] jump = new int[50001];
        int[] res = new int[paint.length];
        
        for (int i = 0; i < paint.length; i++) {
            int start = paint[i][0];
            int end = paint[i][1];
            int count = 0;
            
            while (start < end) {
                if (jump[start] == 0) {
                    count++;
                    jump[start] = end; // Nhảy nhanh nhất cho lần tới
                    start++;
                } else {
                    int nextStart = jump[start];
                    jump[start] = Math.max(jump[start], end);
                    start = nextStart;
                }
            }
            res[i] = count;
        }
        return res;
    }
}
```
**Độ phức tạp:**
- Time: $O(N + M)$ khấu hao (Amortized) do mỗi đoạn nhảy đều nhảy qua khoảng đã tô.
- Space: $O(M)$ với $M$ là giới hạn tọa độ lớn nhất ($50001$).

---

## 29. Maximum Number of Events That Can Be Attended (LeetCode 1353)
**Đề bài chi tiết:** Cho mảng `events` với `events[i] = [start, end]`. Bạn có thể tham gia một sự kiện bất kỳ trong khoảng `[start, end]` (mỗi sự kiện chỉ tốn đúng 1 ngày). Tìm số lượng sự kiện lớn nhất bạn có thể tham gia.

**Phân tích thuật toán:**
- Thuật toán Tham lam (Greedy) kết hợp Priority Queue.
- Sắp xếp sự kiện theo ngày bắt đầu tăng dần.
- Tại ngày `d` bất kỳ, ta cho hết các sự kiện có thể bắt đầu vào ngày `d` vào trong Min-Heap. Min-Heap này sẽ ưu tiên các sự kiện kết thúc SỚM NHẤT để xử lý trước.
- Loại bỏ các sự kiện rác trong Heap đã hết hạn (có `end < d`).
- Nếu Heap không rỗng, lấy sự kiện đỉnh của Heap ra để tham gia trong ngày `d` (tăng số lượng sự kiện tham gia +1). Tiếp tục tăng ngày `d`.

**Mã nguồn Java:**
```java
class Solution {
    public int maxEvents(int[][] events) {
        Arrays.sort(events, (a, b) -> Integer.compare(a[0], b[0]));
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        
        int i = 0, n = events.length, count = 0;
        int d = 1;
        while (i < n || !pq.isEmpty()) {
            if (pq.isEmpty() && i < n && d < events[i][0]) {
                d = events[i][0];
            }
            while (i < n && events[i][0] == d) {
                pq.add(events[i++][1]);
            }
            while (!pq.isEmpty() && pq.peek() < d) {
                pq.poll();
            }
            if (!pq.isEmpty()) {
                pq.poll();
                count++;
            }
            d++;
        }
        return count;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$ cho thao tác sắp xếp mảng và duy trì Heap.
- Space: $O(N)$ lưu trữ trong hàng đợi ưu tiên.

---

## 30. Maximum Number of Events That Can Be Attended II (LeetCode 1751)
**Đề bài chi tiết:** Cho mảng `events` với `events[i] = [start, end, value]`, và số $k$. Bạn được quyền tham gia tối đa $k$ sự kiện không bị chồng chéo để tối đa hóa tổng giá trị thu được (`value`). Trả về giá trị lớn nhất.

**Phân tích thuật toán:**
- Bài toán kết hợp giữa Quy hoạch động (Dynamic Programming) và Tìm kiếm nhị phân (Binary Search). 
- Sắp xếp sự kiện theo thời gian bắt đầu.
- Hàm đệ quy `dfs(index, count)` tính lợi ích lớn nhất nếu xét từ sự kiện `index` và có thể chọn thêm `count` sự kiện nữa.
- Ở mỗi bước, ta có 2 lựa chọn:
  1. Không chọn sự kiện hiện tại, giá trị nhận được là `dfs(index + 1, count)`.
  2. Chọn sự kiện hiện tại, gọi Binary Search tìm sự kiện tiếp theo không bị đè lên sự kiện này (có `start > current.end`). Lợi ích là `current.value + dfs(nextIndex, count - 1)`.
- Dùng một mảng `memo` 2D để nhớ các trạng thái đệ quy.

**Mã nguồn Java:**
```java
class Solution {
    private int[][] memo;
    
    public int maxValue(int[][] events, int k) {
        Arrays.sort(events, (a, b) -> Integer.compare(a[0], b[0]));
        memo = new int[events.length][k + 1];
        for (int[] row : memo) {
            Arrays.fill(row, -1);
        }
        return dfs(events, 0, k);
    }
    
    private int dfs(int[][] events, int index, int k) {
        if (k == 0 || index == events.length) return 0;
        if (memo[index][k] != -1) return memo[index][k];
        
        int skip = dfs(events, index + 1, k);
        
        int nextIndex = binarySearch(events, events[index][1]);
        int take = events[index][2] + dfs(events, nextIndex, k - 1);
        
        memo[index][k] = Math.max(skip, take);
        return memo[index][k];
    }
    
    private int binarySearch(int[][] events, int endTime) {
        int left = 0, right = events.length - 1, res = events.length;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (events[mid][0] > endTime) {
                res = mid;
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return res;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N + N \times K \log N)$ với $N$ là số sự kiện. Có $N \times K$ trạng thái DP, mỗi trạng thái dùng Binary Search $O(\log N)$.
- Space: $O(N \times K)$ cho bảng quy hoạch động và call stack đệ quy.
