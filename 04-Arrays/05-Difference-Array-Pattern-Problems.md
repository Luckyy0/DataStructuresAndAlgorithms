# Companion Problems: 05-Difference-Array-Pattern

Tài liệu này cung cấp **30 bài tập thực hành** về Difference Array (Mảng chênh lệch) từ cơ bản đến nâng cao.

---

## 1. Range Addition (LeetCode 370)
**Đề bài chi tiết:** Cho mảng độ dài $N$ gồm toàn số 0. Cho danh sách các thao tác `updates` trong đó mỗi thao tác là một mảng `[start, end, inc]`, hãy cộng `inc` vào tất cả các phần tử từ chỉ số `start` đến `end`. Trả về mảng kết quả sau tất cả các cập nhật.
**Phân tích thuật toán:** Sử dụng Mảng chênh lệch (Difference Array) kích thước $N+1$. Duyệt qua các updates: `diff[start] += inc`, `diff[end + 1] -= inc`. Cuối cùng tính Prefix Sum của `diff` để khôi phục mảng kết quả.
**Mã nguồn Java:**
```java
public class RangeAddition {
    public int[] getModifiedArray(int length, int[][] updates) {
        int[] diff = new int[length + 1];
        
        // Ghi nhận sự kiện cập nhật
        for (int[] update : updates) {
            int start = update[0];
            int end = update[1];
            int inc = update[2];
            
            diff[start] += inc;
            diff[end + 1] -= inc;
        }
        
        // Khôi phục mảng
        int[] res = new int[length];
        res[0] = diff[0];
        for (int i = 1; i < length; i++) {
            res[i] = res[i - 1] + diff[i];
        }
        
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N + Q)$ với $Q$ là số updates, Space $\mathcal{O}(N)$ cho mảng chênh lệch và mảng kết quả.

---

## 2. Corporate Flight Bookings (LeetCode 1109)
**Đề bài chi tiết:** Có $n$ chuyến bay đánh số từ $1$ đến $n$. Cho danh sách các bookings `[first_i, last_i, seats_i]`, đặt `seats_i` ghế trên mỗi chuyến bay từ $first_i$ đến $last_i$. Trả về mảng đếm tổng số ghế trên mỗi chuyến bay.
**Phân tích thuật toán:** Tương tự bài 1, nhưng chỉ số bắt đầu từ 1. Ta cần trừ 1 để đưa về chỉ số mảng 0-indexed.
**Mã nguồn Java:**
```java
public class FlightBookings {
    public int[] corpFlightBookings(int[][] bookings, int n) {
        int[] diff = new int[n + 1];
        
        for (int[] b : bookings) {
            int start = b[0] - 1; // 0-indexed
            int end = b[1] - 1;
            int seats = b[2];
            
            diff[start] += seats;
            diff[end + 1] -= seats;
        }
        
        int[] res = new int[n];
        res[0] = diff[0];
        for (int i = 1; i < n; i++) {
            res[i] = res[i - 1] + diff[i];
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N + Q)$, Space $\mathcal{O}(N)$.

---

## 3. Car Pooling (LeetCode 1094)
**Đề bài chi tiết:** Bạn lái xe có `capacity` ghế. Cho các chuyến đi `trips[i] = [num_passengers, start_location, end_location]`. Trả về `true` nếu bạn có thể phục vụ tất cả hành khách. (Khách lên ở `start`, xuống ở `end`).
**Phân tích thuật toán:** Max location là 1000. Dùng mảng chênh lệch cỡ 1001. Tại `start`, khách LÊN xe: `diff[start] += num`. Tại `end`, khách XUỐNG xe: `diff[end] -= num`. Sau đó chạy Prefix Sum, nếu tại bất kỳ điểm nào số lượng hành khách $> capacity \rightarrow$ return false.
**Mã nguồn Java:**
```java
public class CarPooling {
    public boolean carPooling(int[][] trips, int capacity) {
        int[] diff = new int[1001]; // Location tối đa là 1000
        
        for (int[] trip : trips) {
            int num = trip[0];
            int start = trip[1];
            int end = trip[2];
            
            diff[start] += num;
            diff[end] -= num; // Trả ghế NGAY tại lúc end, nên là diff[end] chứ ko phải end+1
        }
        
        int passengers = 0;
        for (int i = 0; i <= 1000; i++) {
            passengers += diff[i];
            if (passengers > capacity) {
                return false;
            }
        }
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\max(N, 1000))$, Space $\mathcal{O}(1000) \approx \mathcal{O}(1)$.

---

## 4. Minimum Number of K Consecutive Bit Flips (LeetCode 995)
**Đề bài chi tiết:** Cho mảng nhị phân và số nguyên K. Bạn có thể lật K bit liên tiếp (đảo 0 thành 1 và 1 thành 0). Tìm số lần lật ít nhất để mảng toàn số 1.
**Phân tích thuật toán:** Dùng Difference Array để theo dõi số lần lật (flip). Biến `flipCount` đóng vai trò là Running Sum (Prefix Sum) của mảng chênh lệch. Nếu phần tử hiện tại bị lật chẵn lần mà vẫn là 0 (hoặc lẻ lần mà là 1) $\rightarrow$ Cần 1 thao tác lật bắt đầu từ đây (tới i+K).
**Mã nguồn Java:**
```java
public class MinKBitFlips {
    public int minKBitFlips(int[] nums, int k) {
        int n = nums.length;
        int[] diff = new int[n + 1];
        int flipCount = 0; // Running sum of flips
        int ans = 0;
        
        for (int i = 0; i < n; i++) {
            flipCount += diff[i];
            // Nếu (số 0 và số lần lật chẵn) hoặc (số 1 và lật lẻ) => phải lật
            if ((nums[i] == 0 && flipCount % 2 == 0) || (nums[i] == 1 && flipCount % 2 != 0)) {
                if (i + k > n) return -1; // Không đủ không gian để lật k bit
                flipCount++;
                diff[i + k]--; // Đánh dấu kết thúc lật tại i+k
                ans++;
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$. (Có thể tối ưu $\mathcal{O}(1)$ space bằng cách mượn mảng `nums`).

---

## 5. Sweep Line (TreeMap) - My Calendar III (LeetCode 732)
**Đề bài chi tiết:** Có nhiều sự kiện được thêm vào (startTime, endTime). Tìm số lượng sự kiện chéo nhau nhiều nhất tại MỌI thời điểm. Giới hạn `startTime, endTime` lên tới $10^9$.
**Phân tích thuật toán:** Vì tọa độ tới $10^9$, dùng mảng sẽ bị Memory Limit Exceeded. Dùng `TreeMap` để lưu trữ Mảng Chênh Lệch Dạng Thưa (Sparse Difference Array). `map.put(start, get + 1)`, `map.put(end, get - 1)`. Biến chạy quét qua map tính max.
**Mã nguồn Java:**
```java
import java.util.TreeMap;

class MyCalendarThree {
    private TreeMap<Integer, Integer> timeline;

    public MyCalendarThree() {
        timeline = new TreeMap<>();
    }
    
    public int book(int start, int end) {
        timeline.put(start, timeline.getOrDefault(start, 0) + 1);
        timeline.put(end, timeline.getOrDefault(end, 0) - 1);
        
        int active = 0, maxActive = 0;
        for (int delta : timeline.values()) {
            active += delta;
            maxActive = Math.max(maxActive, active);
        }
        return maxActive;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log N)$ (Với N là số lượng event hiện tại), Space $\mathcal{O}(N)$.

---

## 6. Shifting Letters II (LeetCode 2381)
**Đề bài chi tiết:** Cho chuỗi $s$ và mảng các `shifts`. Mỗi `shift = [start, end, direction]`. Hướng 1 là shift tới (a $\rightarrow$ b), hướng 0 là lùi (b $\rightarrow$ a). Áp dụng tất cả shift, trả về chuỗi mới.
**Phân tích thuật toán:** Dùng Difference Array để tích lũy tổng số `shift` cho từng kí tự. Shift forward (+1), backward (-1). Sau khi tính toán ra mảng `shift` cuối cùng, xử lý Modulo 26 và áp dụng cho từng kí tự.
**Mã nguồn Java:**
```java
public class ShiftingLettersII {
    public String shiftingLetters(String s, int[][] shifts) {
        int n = s.length();
        int[] diff = new int[n + 1];
        
        for (int[] shift : shifts) {
            int dir = shift[2] == 1 ? 1 : -1;
            diff[shift[0]] += dir;
            diff[shift[1] + 1] -= dir;
        }
        
        char[] chars = s.toCharArray();
        int currentShift = 0;
        for (int i = 0; i < n; i++) {
            currentShift += diff[i];
            int modShift = (currentShift % 26 + 26) % 26; // Xử lý âm
            chars[i] = (char) ('a' + (chars[i] - 'a' + modShift) % 26);
        }
        
        return new String(chars);
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N + Q)$, Space $\mathcal{O}(N)$.

---

## 7. 2D Difference Array Template
**Đề bài chi tiết:** Template cơ bản để update một vùng ma trận chữ nhật $(r1, c1)$ đến $(r2, c2)$ bằng giá trị $V$.
**Phân tích thuật toán:** Giống bao hàm loại trừ của Prefix Sum. 4 thao tác update:
1. `diff[r1][c1] += V`
2. `diff[r2+1][c1] -= V`
3. `diff[r1][c2+1] -= V`
4. `diff[r2+1][c2+1] += V`
Khôi phục bằng cách lấy Prefix Sum 2D.
**Mã nguồn Java:**
```java
public class Diff2D {
    int[][] diff;
    int m, n;
    
    public Diff2D(int rows, int cols) {
        m = rows; n = cols;
        diff = new int[m + 1][n + 1];
    }
    
    public void add(int r1, int c1, int r2, int c2, int val) {
        diff[r1][c1] += val;
        diff[r2 + 1][c1] -= val;
        diff[r1][c2 + 1] -= val;
        diff[r2 + 1][c2 + 1] += val;
    }
    
    public int[][] compute() {
        int[][] res = new int[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                res[i][j] = diff[i][j];
                if (i > 0) res[i][j] += res[i-1][j];
                if (j > 0) res[i][j] += res[i][j-1];
                if (i > 0 && j > 0) res[i][j] -= res[i-1][j-1];
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Update $\mathcal{O}(1)$, Build Matrix $\mathcal{O}(MN)$.

---

## 8. Describe the Painting (LeetCode 1943)
**Đề bài chi tiết:** Tô màu một dòng thẳng bằng nhiều đoạn `[start, end, color]`. Một vị trí có thể có nhiều màu hòa trộn (bằng cách cộng tổng color). Trả về các đoạn đã tô.
**Phân tích thuật toán:** Sparse Difference Array bằng `TreeMap` để đối phó tọa độ lớn. Khi `color` update kết thúc ở `end`, ta không gộp nó với đoạn bắt đầu ở `end`. Phải tách các "Key points" cẩn thận khi loop qua map để gom thành từng đoạn (Segment).
**Mã nguồn Java:**
```java
import java.util.*;

public class DescribePainting {
    public List<List<Long>> splitPainting(int[][] segments) {
        TreeMap<Integer, Long> map = new TreeMap<>();
        for (int[] seg : segments) {
            map.put(seg[0], map.getOrDefault(seg[0], 0L) + seg[2]);
            map.put(seg[1], map.getOrDefault(seg[1], 0L) - seg[2]);
        }
        
        List<List<Long>> res = new ArrayList<>();
        int prevEvent = -1;
        long currentMix = 0;
        
        for (Map.Entry<Integer, Long> entry : map.entrySet()) {
            int pos = entry.getKey();
            long delta = entry.getValue();
            
            if (currentMix > 0 && prevEvent != -1) {
                res.add(Arrays.asList((long) prevEvent, (long) pos, currentMix));
            }
            currentMix += delta;
            prevEvent = pos;
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log N)$, Space $\mathcal{O}(N)$.

---

## 9. Maximum Population Year (LeetCode 1854)
**Đề bài chi tiết:** Cho danh sách logs gồm năm sinh và năm mất của n người. Tìm năm có số dân số đông nhất.
**Phân tích thuật toán:** Năm hợp lệ nằm trong khoảng 1950 đến 2050. Mảng chênh lệch dài $2051 - 1950 = 101$. Năm sinh `diff[birth]++`, năm mất `diff[death]--` (Tại năm mất người đó không còn). Quét lại mảng để tìm Max.
**Mã nguồn Java:**
```java
public class MaxPopulationYear {
    public int maximumPopulation(int[][] logs) {
        int[] diff = new int[101]; // Từ 1950 - 2050
        
        for (int[] log : logs) {
            diff[log[0] - 1950]++;
            diff[log[1] - 1950]--;
        }
        
        int maxPop = 0;
        int currentPop = 0;
        int maxYear = 1950;
        
        for (int i = 0; i < 101; i++) {
            currentPop += diff[i];
            if (currentPop > maxPop) {
                maxPop = currentPop;
                maxYear = i + 1950;
            }
        }
        return maxYear;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$ (Do không gian giới hạn 101).

---

## 10. Amount of New Area Painted Each Day (LeetCode 2158)
**Đề bài chi tiết:** Mỗi ngày bạn sơn đoạn `[start, end]`. Tính độ dài đoạn mới được sơn mỗi ngày.
**Phân tích thuật toán:** Mặc dù giống Sweep Line, nhưng yêu cầu xuất KQ TỪNG NGÀY nên Sweep Line mảng chênh lệch cuối không thỏa. Có thể giải bằng Segment Tree, hoặc dùng mảng DSU (Disjoint Set) - nhảy (Jump) qua vùng đã tô (Path compression). (Đây là biến thể bài Interval).
**Mã nguồn Java:** (DSU jump)
```java
public class NewAreaPainted {
    public int[] amountPainted(int[][] paint) {
        int[] jump = new int[50001];
        int[] res = new int[paint.length];
        
        for (int i = 0; i < paint.length; i++) {
            int start = paint[i][0];
            int end = paint[i][1];
            int count = 0;
            
            while (start < end) {
                int next = Math.max(start + 1, jump[start]);
                if (jump[start] == 0) {
                    count++;
                    jump[start] = end; // Rút ngắn (Shortcut) cho lần sau
                } else {
                    jump[start] = Math.max(jump[start], end);
                }
                start = next;
            }
            res[i] = count;
        }
        return res;
    }
}
```
**Độ phức tạp:** Amortized $\mathcal{O}(N + \text{maxRange})$.

---

## 11. Maximum Sum of 3 Non-Overlapping Subarrays (LeetCode 689)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` và số nguyên `k`. Tìm 3 mảng con không giao nhau, mỗi mảng có độ dài `k`, sao cho tổng các phần tử trong 3 mảng con này là lớn nhất. Trả về mảng chứa 3 chỉ số bắt đầu của 3 mảng con đó. Nếu có nhiều đáp án, trả về đáp án có thứ tự từ điển nhỏ nhất.
**Phân tích thuật toán:** Mặc dù thường giải bằng Quy hoạch động (DP), ta cũng có thể coi việc chọn mảng con như một mảng trượt (sliding window) - tương đương với mảng chênh lệch (Prefix Sum). Đầu tiên, tính mảng tổng của mọi mảng con độ dài `k`. Sau đó dùng mảng `left` và `right` để lưu chỉ số của mảng con có tổng lớn nhất từ trái sang và từ phải sang. Cuối cùng, duyệt vị trí của mảng con ở giữa để tìm tổng lớn nhất của cả 3.
**Mã nguồn Java:**
```java
public class MaxSumOfThreeSubarrays {
    public int[] maxSumOfThreeSubarrays(int[] nums, int k) {
        int n = nums.length;
        int[] sum = new int[n + 1];
        for (int i = 0; i < n; i++) {
            sum[i + 1] = sum[i] + nums[i];
        }
        
        int[] w = new int[n - k + 1];
        for (int i = 0; i < w.length; i++) {
            w[i] = sum[i + k] - sum[i];
        }
        
        int[] left = new int[w.length];
        int best = 0;
        for (int i = 0; i < w.length; i++) {
            if (w[i] > w[best]) best = i;
            left[i] = best;
        }
        
        int[] right = new int[w.length];
        best = w.length - 1;
        for (int i = w.length - 1; i >= 0; i--) {
            if (w[i] >= w[best]) best = i;
            right[i] = best;
        }
        
        int[] ans = new int[]{-1, -1, -1};
        int maxSum = 0;
        
        for (int j = k; j < w.length - k; j++) {
            int i = left[j - k];
            int l = right[j + k];
            int total = w[i] + w[j] + w[l];
            if (total > maxSum) {
                maxSum = total;
                ans[0] = i;
                ans[1] = j;
                ans[2] = l;
            }
        }
        
        return ans;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 12. Minimum Number of Increments on Subarrays to Form a Target Array (LeetCode 1526)
**Đề bài chi tiết:** Cho một mảng `target` gồm các số nguyên dương. Bạn khởi tạo một mảng `initial` có cùng độ dài gồm toàn số 0. Mỗi bước, bạn có thể chọn một mảng con và tăng mỗi phần tử trong mảng con đó lên 1. Tìm số bước tối thiểu để mảng ban đầu trở thành mảng `target`.
**Phân tích thuật toán:** Sử dụng Mảng chênh lệch (Difference Array). Xét `diff[i] = target[i] - target[i-1]`. Mỗi thao tác tăng một mảng con `[L, R]` lên 1 sẽ làm `diff[L]` tăng 1 và `diff[R+1]` giảm 1. Để tạo ra mảng `target`, ta chỉ cần quan tâm đến các giá trị `diff[i] > 0` (vì các giá trị âm sẽ tự động được triệt tiêu khi ta kết thúc thao tác tăng mảng con). Do đó, tổng số thao tác chính là tổng các `diff[i]` dương.
**Mã nguồn Java:**
```java
public class MinNumberOperations {
    public int minNumberOperations(int[] target) {
        int res = target[0];
        for (int i = 1; i < target.length; i++) {
            // Chỉ cộng thêm lượng tăng mới (Difference > 0)
            if (target[i] > target[i - 1]) {
                res += target[i] - target[i - 1];
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 13. Check if All the Integers in a Range Are Covered (LeetCode 1893)
**Đề bài chi tiết:** Cho mảng 2D `ranges` trong đó `ranges[i] = [start_i, end_i]` và hai số nguyên `left`, `right`. Trả về `true` nếu mọi số nguyên trong đoạn `[left, right]` đều được bao phủ bởi ít nhất một khoảng trong `ranges`, ngược lại trả về `false`.
**Phân tích thuật toán:** Mảng đánh dấu có kích thước nhỏ (tối đa 51). Sử dụng Mảng chênh lệch (Difference Array). Với mỗi `[start, end]`, `diff[start]++` và `diff[end + 1]--`. Sau đó, tính Prefix Sum của `diff` để biết số lượng khoảng bao phủ mỗi điểm. Cuối cùng, kiểm tra xem mọi điểm từ `left` đến `right` có giá trị Prefix Sum $> 0$ hay không.
**Mã nguồn Java:**
```java
public class RangeCovered {
    public boolean isCovered(int[][] ranges, int left, int right) {
        int[] diff = new int[52];
        for (int[] range : ranges) {
            diff[range[0]]++;
            diff[range[1] + 1]--;
        }
        
        int overlap = 0;
        for (int i = 1; i <= right; i++) {
            overlap += diff[i];
            if (i >= left && overlap == 0) {
                return false;
            }
        }
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N + M)$ với $N$ là số khoảng, $M$ là giá trị tối đa (50), Space $\mathcal{O}(M)$.

---

## 14. My Calendar II (LeetCode 731)
**Đề bài chi tiết:** Viết một lớp `MyCalendarTwo` để lưu các sự kiện `(start, end)`. Không được phép có 3 sự kiện trùng nhau tại bất kỳ thời điểm nào (triple booking). Nếu một sự kiện mới gây ra triple booking, trả về `false` và không thêm vào lịch. Ngược lại, thêm sự kiện và trả về `true`.
**Phân tích thuật toán:** Sử dụng Mảng chênh lệch dạng thưa (Sparse Difference Array) thông qua `TreeMap`. Khi thêm một sự kiện mới, ta tạm thời cập nhật `map.put(start, +1)` và `map.put(end, -1)`. Sau đó duyệt qua `TreeMap` để tính Prefix Sum (số sự kiện chéo nhau). Nếu giá trị này đạt tới 3, ta lùi lại (revert) bằng cách trừ đi và trả về `false`. Nếu không, sự kiện hợp lệ, trả về `true`.
**Mã nguồn Java:**
```java
import java.util.TreeMap;

class MyCalendarTwo {
    private TreeMap<Integer, Integer> map;

    public MyCalendarTwo() {
        map = new TreeMap<>();
    }
    
    public boolean book(int start, int end) {
        map.put(start, map.getOrDefault(start, 0) + 1);
        map.put(end, map.getOrDefault(end, 0) - 1);
        
        int active = 0;
        for (int delta : map.values()) {
            active += delta;
            if (active >= 3) {
                // Revert
                map.put(start, map.get(start) - 1);
                map.put(end, map.get(end) + 1);
                if (map.get(start) == 0) map.remove(start);
                if (map.get(end) == 0) map.remove(end);
                return false;
            }
        }
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$ cho mỗi lần `book` với $N$ là số sự kiện hiện tại, Space $\mathcal{O}(N)$.

---

## 15. Count Ways to Make Array With Product (LeetCode 1735)
**Đề bài chi tiết:** Cho một mảng các truy vấn `queries[i] = [n, k]`. Bạn cần tạo một mảng gồm `n` số nguyên dương sao cho tích của chúng bằng `k`. Trả về số cách tạo mảng đó cho mỗi truy vấn, lấy modulo $10^9 + 7$.
**Phân tích thuật toán:** Dù bài này liên quan nhiều đến Toán học (Phân tích thừa số nguyên tố và Tổ hợp), nhưng tư tưởng phân phối các thừa số vào các "ngăn" (vị trí trong mảng) cũng có thể mở rộng từ các bài toán đếm khoảng. Với mỗi thừa số nguyên tố của `k` xuất hiện `count` lần, ta cần chia `count` vật giống nhau vào `n` ngăn khác nhau (có thể rỗng). Số cách là $C(count + n - 1, n - 1)$. Kết quả là tích của số cách cho tất cả các thừa số nguyên tố.
**Mã nguồn Java:**
```java
import java.util.*;

public class WaysToMakeArrayWithProduct {
    private static final int MOD = 1_000_000_007;
    private long[] fact, invFact;
    
    public int[] waysToFillArray(int[][] queries) {
        int maxN = 10000 + 15;
        fact = new long[maxN];
        invFact = new long[maxN];
        fact[0] = invFact[0] = 1;
        for (int i = 1; i < maxN; i++) {
            fact[i] = (fact[i - 1] * i) % MOD;
            invFact[i] = power(fact[i], MOD - 2);
        }
        
        int[] res = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            int n = queries[i][0], k = queries[i][1];
            long ways = 1;
            for (int p = 2; p * p <= k; p++) {
                if (k % p == 0) {
                    int count = 0;
                    while (k % p == 0) { count++; k /= p; }
                    ways = (ways * nCr(count + n - 1, n - 1)) % MOD;
                }
            }
            if (k > 1) {
                ways = (ways * nCr(1 + n - 1, n - 1)) % MOD;
            }
            res[i] = (int) ways;
        }
        return res;
    }
    
    private long nCr(int n, int r) {
        if (r < 0 || r > n) return 0;
        long num = fact[n];
        long den = (invFact[r] * invFact[n - r]) % MOD;
        return (num * den) % MOD;
    }
    
    private long power(long base, long exp) {
        long res = 1;
        base %= MOD;
        while (exp > 0) {
            if ((exp & 1) == 1) res = (res * base) % MOD;
            base = (base * base) % MOD;
            exp >>= 1;
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(Q \sqrt{K})$, Space $\mathcal{O}(M)$ cho mảng giai thừa.

---

## 16. Divide Intervals Into Minimum Number of Groups (LeetCode 2406)
**Đề bài chi tiết:** Cho mảng 2D `intervals` trong đó `intervals[i] = [left_i, right_i]`. Hãy chia các khoảng này thành số lượng nhóm tối thiểu sao cho trong mỗi nhóm, không có hai khoảng nào giao nhau.
**Phân tích thuật toán:** Bài toán này tương đương với việc tìm số lượng khoảng giao nhau lớn nhất tại MỘT thời điểm bất kỳ (Độ dày Max). Sử dụng Mảng chênh lệch (Difference Array) hoặc Sweep Line. Do giới hạn `left, right` lớn (lên tới $10^6$), ta có thể dùng mảng kích thước $10^6 + 2$. Với mỗi khoảng `[L, R]`, `diff[L]++` và `diff[R+1]--`. Sau đó cộng dồn để tìm max.
**Mã nguồn Java:**
```java
public class MinGroups {
    public int minGroups(int[][] intervals) {
        int[] diff = new int[1000005]; // Giới hạn max là 10^6
        
        for (int[] interval : intervals) {
            diff[interval[0]]++;
            diff[interval[1] + 1]--;
        }
        
        int maxGroups = 0;
        int current = 0;
        for (int i = 0; i <= 1000001; i++) {
            current += diff[i];
            maxGroups = Math.max(maxGroups, current);
        }
        
        return maxGroups;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N + M)$ với $N$ là số khoảng, $M$ là giá trị tối đa $10^6$. Space $\mathcal{O}(M)$.

---

## 17. Zero Array Transformation I (LeetCode 3355)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` và mảng 2D `queries`, mỗi `query = [l, r]`. Với mỗi `query`, bạn có thể trừ 1 vào các phần tử trong khoảng `[l, r]`. Bạn có thể áp dụng các `queries` hoặc bỏ qua. Trả về `true` nếu bạn có thể biến mảng `nums` thành mảng có tất cả phần tử $\le 0$.
**Phân tích thuật toán:** Sử dụng Mảng chênh lệch (Difference Array) để theo dõi tổng số lần ta CÓ THỂ trừ đi ở mỗi vị trí. Về cơ bản, ta cho rằng tất cả các query đều được áp dụng. Mảng chênh lệch `diff[l]++`, `diff[r+1]--`. Sau đó tính Prefix Sum mảng `diff` để biết mỗi vị trí $i$ có thể bị giảm tối đa bao nhiêu. Cuối cùng, duyệt mảng `nums`, nếu có bất kỳ `nums[i]` nào lớn hơn mức giảm tối đa tại đó thì trả về `false`.
**Mã nguồn Java:**
```java
public class ZeroArrayI {
    public boolean isZeroArray(int[] nums, int[][] queries) {
        int n = nums.length;
        int[] diff = new int[n + 1];
        
        for (int[] q : queries) {
            diff[q[0]]++;
            diff[q[1] + 1]--;
        }
        
        int decrement = 0;
        for (int i = 0; i < n; i++) {
            decrement += diff[i];
            if (nums[i] > decrement) {
                return false;
            }
        }
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N + Q)$, Space $\mathcal{O}(N)$.

---

## 18. Zero Array Transformation II (LeetCode 3356)
**Đề bài chi tiết:** Giống bài 17, nhưng lần này `queries[i] = [l, r, val]`, nghĩa là bạn có thể giảm các phần tử trong `[l, r]` đi tối đa `val`. Bạn phải chọn một số nguyên $K$ nhỏ nhất sao cho chỉ dùng $K$ queries ĐẦU TIÊN là có thể biến tất cả phần tử của `nums` thành $\le 0$. Nếu không thể, trả về -1.
**Phân tích thuật toán:** Nếu ta dùng $K$ queries đầu tiên mà thỏa mãn, thì $K+1$ cũng thỏa mãn. Đây là tính chất đơn điệu, do đó ta có thể dùng Tìm kiếm nhị phân (Binary Search) trên $K$ (từ 0 đến số lượng queries). Với mỗi $K$ được thử, ta dùng Mảng chênh lệch để áp dụng $K$ queries đầu tiên và kiểm tra xem mảng `nums` có về $\le 0$ hay không.
**Mã nguồn Java:**
```java
public class ZeroArrayII {
    public int minZeroArray(int[] nums, int[][] queries) {
        int left = 0;
        int right = queries.length;
        int ans = -1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (canZero(nums, queries, mid)) {
                ans = mid;
                right = mid - 1; // Thử tìm K nhỏ hơn
            } else {
                left = mid + 1;
            }
        }
        return ans;
    }
    
    private boolean canZero(int[] nums, int[][] queries, int k) {
        int n = nums.length;
        long[] diff = new long[n + 1]; // Tránh tràn số
        
        for (int i = 0; i < k; i++) {
            diff[queries[i][0]] += queries[i][2];
            diff[queries[i][1] + 1] -= queries[i][2];
        }
        
        long decrement = 0;
        for (int i = 0; i < n; i++) {
            decrement += diff[i];
            if (nums[i] > decrement) {
                return false;
            }
        }
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}((N + Q) \log Q)$, Space $\mathcal{O}(N)$.

---

## 19. Stamping the Sequence (LeetCode 936)
**Đề bài chi tiết:** Cho chuỗi `stamp` và chuỗi `target`. Ban đầu bạn có một chuỗi `s` toàn ký tự `?` dài bằng `target`. Mỗi lần đóng dấu, bạn có thể thay thế một đoạn dài bằng `stamp` thành `stamp`. Tìm một chuỗi các chỉ số bắt đầu để đóng dấu sao cho tạo thành `target`. Trả về mảng rỗng nếu không thể.
**Phân tích thuật toán:** Bài toán này là Reverse Greedy (Tham lam ngược). Ta tìm các vị trí trong `target` khớp với `stamp` (các ký tự `?` được coi là khớp với mọi thứ). Khi tìm thấy, ta biến đổi đoạn đó thành `?` (tương đương với việc tháo dấu ra) và ghi nhận. Nếu toàn bộ `target` trở thành `?`, ta đảo ngược mảng kết quả. Mảng chênh lệch không trực tiếp giải quyết bài này, nhưng tư duy xử lý khoảng (Interval updates to `?`) có cấu trúc tương tự việc lật khoảng (flipping intervals).
**Mã nguồn Java:**
```java
import java.util.*;

public class StampingSequence {
    public int[] movesToStamp(String stamp, String target) {
        char[] S = stamp.toCharArray();
        char[] T = target.toCharArray();
        List<Integer> res = new ArrayList<>();
        boolean[] visited = new boolean[T.length];
        int stars = 0;
        
        while (stars < T.length) {
            boolean replaced = false;
            for (int i = 0; i <= T.length - S.length; i++) {
                if (!visited[i] && canReplace(T, i, S)) {
                    stars = doReplace(T, i, S.length, stars);
                    replaced = true;
                    visited[i] = true;
                    res.add(i);
                    if (stars == T.length) break;
                }
            }
            if (!replaced) return new int[0]; // Không thể thay thế thêm
        }
        
        int[] ans = new int[res.size()];
        for (int i = 0; i < res.size(); i++) {
            ans[i] = res.get(res.size() - 1 - i); // Đảo ngược
        }
        return ans;
    }
    
    private boolean canReplace(char[] T, int pos, char[] S) {
        for (int i = 0; i < S.length; i++) {
            if (T[pos + i] != '*' && T[pos + i] != S[i]) {
                return false;
            }
        }
        return true;
    }
    
    private int doReplace(char[] T, int pos, int len, int stars) {
        for (int i = 0; i < len; i++) {
            if (T[pos + i] != '*') {
                T[pos + i] = '*';
                stars++;
            }
        }
        return stars;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N^2 \cdot M)$ với $N$ là độ dài `target`, $M$ là độ dài `stamp`, Space $\mathcal{O}(N)$.

---

## 20. Range Sum Query - Mutable (LeetCode 307)
**Đề bài chi tiết:** Cần thiết kế một cấu trúc dữ liệu cho phép 2 loại thao tác: Cập nhật giá trị một phần tử tại vị trí `index` thành `val`, và Tính tổng các phần tử trong khoảng `[left, right]`.
**Phân tích thuật toán:** Mảng chênh lệch truyền thống không thể xử lý truy vấn động đan xen (update một phần tử và lấy prefix sum liên tục). Bài toán này yêu cầu Binary Indexed Tree (Fenwick Tree) hoặc Segment Tree. Fenwick Tree lưu trữ tổng của các khoảng theo sự chênh lệch của các bit nhị phân, là công cụ mở rộng mạnh mẽ khi Prefix Sum tĩnh không đủ.
**Mã nguồn Java:**
```java
class NumArray {
    int[] BIT;
    int[] nums;
    int n;

    public NumArray(int[] nums) {
        this.nums = nums;
        this.n = nums.length;
        BIT = new int[n + 1];
        for (int i = 0; i < n; i++) {
            init(i, nums[i]);
        }
    }
    
    private void init(int i, int val) {
        i++;
        while (i <= n) {
            BIT[i] += val;
            i += (i & -i); // Di chuyển tới nút cha
        }
    }
    
    public void update(int index, int val) {
        int diff = val - nums[index];
        nums[index] = val;
        init(index, diff); // Cập nhật mảng BIT bằng lượng chênh lệch (diff)
    }
    
    public int sumRange(int left, int right) {
        return getSum(right) - getSum(left - 1);
    }
    
    private int getSum(int i) {
        int sum = 0;
        i++;
        while (i > 0) {
            sum += BIT[i];
            i -= (i & -i); // Lùi về cây con bên trái
        }
        return sum;
    }
}
```
**Độ phức tạp:** Update $\mathcal{O}(\log N)$, Query $\mathcal{O}(\log N)$, Space $\mathcal{O}(N)$.

---

## 21. Number of Flowers in Full Bloom (LeetCode 2251)
**Đề bài chi tiết:** Cho mảng 2D `flowers` trong đó `flowers[i] = [start_i, end_i]` biểu thị khoảng thời gian hoa thứ `i` nở và tàn. Bạn cũng có mảng `people` với `people[j]` là thời gian người thứ `j` đến xem hoa. Trả về mảng số lượng hoa đang nở vào thời điểm mỗi người đến.
**Phân tích thuật toán:** Tọa độ thời gian có thể lên tới $10^9$. Ta không thể dùng mảng tĩnh. Sử dụng `TreeMap` để làm Mảng chênh lệch (Sparse Difference Array). `map.put(start, +1)`, `map.put(end + 1, -1)` (hoa tàn ngay sau `end`). Chạy Sweep Line để tính tổng cộng dồn (Prefix Sum) tạo thành danh sách các điểm thay đổi. Sau đó với mỗi người trong `people`, dùng Binary Search (tìm kiếm nhị phân) trên danh sách điểm để lấy ra số hoa nở.
**Mã nguồn Java:**
```java
import java.util.*;

public class FlowersInFullBloom {
    public int[] fullBloomFlowers(int[][] flowers, int[] people) {
        TreeMap<Integer, Integer> diff = new TreeMap<>();
        for (int[] f : flowers) {
            diff.put(f[0], diff.getOrDefault(f[0], 0) + 1);
            diff.put(f[1] + 1, diff.getOrDefault(f[1] + 1, 0) - 1);
        }
        
        List<Integer> positions = new ArrayList<>();
        List<Integer> prefix = new ArrayList<>();
        int curr = 0;
        
        for (Map.Entry<Integer, Integer> entry : diff.entrySet()) {
            positions.add(entry.getKey());
            curr += entry.getValue();
            prefix.add(curr);
        }
        
        int[] res = new int[people.length];
        for (int i = 0; i < people.length; i++) {
            res[i] = binarySearch(positions, prefix, people[i]);
        }
        
        return res;
    }
    
    private int binarySearch(List<Integer> positions, List<Integer> prefix, int target) {
        int left = 0, right = positions.size() - 1;
        int ans = 0;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (positions.get(mid) <= target) {
                ans = prefix.get(mid);
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log N + M \log N)$, Space $\mathcal{O}(N)$.

---

## 22. Maximum Beauty of an Array After Applying Operation (LeetCode 2779)
**Đề bài chi tiết:** Cho mảng `nums` và số nguyên `k`. Trong mỗi thao tác, bạn có thể thay thế `nums[i]` bằng một số nằm trong khoảng `[nums[i] - k, nums[i] + k]`. Tìm độ dài lớn nhất của một subsequence (mảng con không liên tiếp) sao cho tất cả các phần tử đều bằng nhau.
**Phân tích thuật toán:** Thay vì biến đổi số, ta coi mỗi số `nums[i]` là một khoảng `[nums[i] - k, nums[i] + k]`. Bài toán quy về: Tìm một điểm bất kỳ được bao phủ bởi số lượng khoảng nhiều nhất (Độ dày giao nhau lớn nhất). Vì giá trị lớn nhất trong mảng khoảng $10^5$, ta có thể dùng Mảng chênh lệch tĩnh với kích thước $10^5 + 2k + 2$.
**Mã nguồn Java:**
```java
public class MaximumBeauty {
    public int maximumBeauty(int[] nums, int k) {
        int maxVal = 0;
        for (int x : nums) {
            maxVal = Math.max(maxVal, x);
        }
        
        int[] diff = new int[maxVal + 2 * k + 2];
        for (int x : nums) {
            int left = Math.max(0, x - k);
            int right = x + k;
            diff[left]++;
            diff[right + 1]--;
        }
        
        int maxCount = 0;
        int current = 0;
        for (int i = 0; i < diff.length; i++) {
            current += diff[i];
            maxCount = Math.max(maxCount, current);
        }
        
        return maxCount;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N + M)$ với $M$ là giá trị tối đa, Space $\mathcal{O}(M)$.

---

## 23. Meeting Rooms II (LeetCode 253)
**Đề bài chi tiết:** Cho mảng các khoảng thời gian họp `intervals[i] = [start, end]`. Tìm số lượng phòng họp tối thiểu cần thiết để sắp xếp mọi cuộc họp mà không bị trùng lặp thời gian trong cùng một phòng.
**Phân tích thuật toán:** Số phòng họp tối thiểu chính là số lượng cuộc họp diễn ra đồng thời lớn nhất tại bất kỳ thời điểm nào. Ta áp dụng Sweep Line / Difference Array: Với mỗi khoảng, đánh dấu bắt đầu là `+1` (thêm một phòng) và kết thúc là `-1` (trả lại phòng). Sắp xếp các mốc thời gian và tính cộng dồn.
**Mã nguồn Java:**
```java
import java.util.*;

public class MeetingRoomsII {
    public int minMeetingRooms(int[][] intervals) {
        TreeMap<Integer, Integer> map = new TreeMap<>();
        for (int[] interval : intervals) {
            map.put(interval[0], map.getOrDefault(interval[0], 0) + 1);
            map.put(interval[1], map.getOrDefault(interval[1], 0) - 1);
        }
        
        int maxRooms = 0;
        int currentRooms = 0;
        for (int delta : map.values()) {
            currentRooms += delta;
            maxRooms = Math.max(maxRooms, currentRooms);
        }
        
        return maxRooms;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log N)$, Space $\mathcal{O}(N)$.

---

## 24. Number of Ships in a Rectangle (LeetCode 1274)
**Đề bài chi tiết:** (Bài toán Interactive) Biển được biểu diễn bằng tọa độ 2D. Tàu nằm ở các tọa độ nguyên. Bạn được cung cấp API `hasShips(topRight, bottomLeft)` trả về `true` nếu có ít nhất một tàu trong hình chữ nhật này. Đếm tổng số tàu (tối đa 10 tàu).
**Phân tích thuật toán:** Mặc dù không sử dụng thuần Mảng chênh lệch, đây là bài toán kiểm tra độ bao phủ 2D cực mạnh. Ta dùng Chia để trị (Divide and Conquer). Tại mỗi bước, chia hình chữ nhật lớn thành 4 hình chữ nhật nhỏ (phần tư). Nếu `hasShips` trả về `false`, không cần duyệt tiếp phần đó. Nếu là 1 điểm và có tàu, cộng 1 vào kết quả.
**Mã nguồn Java:**
```java
/**
 * // This is Sea's API interface.
 * // You should not implement it, or speculate about its implementation
 * class Sea {
 *     public boolean hasShips(int[] topRight, int[] bottomLeft);
 * }
 */

class Solution {
    public int countShips(Sea sea, int[] topRight, int[] bottomLeft) {
        // Base case: Nếu hình chữ nhật không hợp lệ hoặc không có tàu
        if (bottomLeft[0] > topRight[0] || bottomLeft[1] > topRight[1]) return 0;
        if (!sea.hasShips(topRight, bottomLeft)) return 0;
        
        // Base case: Nếu là một điểm duy nhất
        if (topRight[0] == bottomLeft[0] && topRight[1] == bottomLeft[1]) return 1;
        
        // Chia thành 4 vùng
        int midX = (topRight[0] + bottomLeft[0]) / 2;
        int midY = (topRight[1] + bottomLeft[1]) / 2;
        
        return countShips(sea, new int[]{midX, midY}, bottomLeft) +
               countShips(sea, topRight, new int[]{midX + 1, midY + 1}) +
               countShips(sea, new int[]{midX, topRight[1]}, new int[]{bottomLeft[0], midY + 1}) +
               countShips(sea, new int[]{topRight[0], midY}, new int[]{midX + 1, bottomLeft[1]});
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\log(\text{Width} \cdot \text{Height}))$, Space $\mathcal{O}(\log(\text{Area}))$. Do tàu rất thưa (Max 10), số lần gọi API là cực nhỏ.

---

## 25. Maximize the Beauty of the Garden (LeetCode 1788)
**Đề bài chi tiết:** Cho mảng số nguyên `flowers`. Vẻ đẹp của khu vườn là tổng giá trị của các bông hoa. Bạn có thể nhổ đi một số bông hoa, với điều kiện khu vườn còn lại phải có hoa đầu tiên và hoa cuối cùng CÙNG một loại (giá trị giống nhau). Tìm vẻ đẹp lớn nhất có thể.
**Phân tích thuật toán:** Vì có thể bỏ qua các bông hoa có vẻ đẹp âm, phần tử ở giữa nếu dương ta sẽ giữ, nếu âm ta sẽ bỏ. Do đó, bài toán quy về: Sử dụng Prefix Sum chỉ lưu tổng các số dương (`prefixPositive`). Với mỗi phần tử, tìm lần xuất hiện đầu tiên của nó thông qua HashMap, và cộng tổng các phần tử dương giữa chúng (cộng thêm 2 phần tử đầu cuối dù chúng có âm hay dương).
**Mã nguồn Java:**
```java
import java.util.*;

public class MaxBeautyGarden {
    public int maximumBeauty(int[] flowers) {
        int n = flowers.length;
        int[] prefixPos = new int[n + 1];
        
        for (int i = 0; i < n; i++) {
            prefixPos[i + 1] = prefixPos[i] + Math.max(0, flowers[i]);
        }
        
        Map<Integer, Integer> firstSeen = new HashMap<>();
        int maxBeauty = Integer.MIN_VALUE;
        
        for (int i = 0; i < n; i++) {
            int val = flowers[i];
            if (firstSeen.containsKey(val)) {
                int firstIdx = firstSeen.get(val);
                // Vẻ đẹp = tổng positive ở giữa + 2 phần tử đầu cuối
                int beauty = val * 2 + (prefixPos[i] - prefixPos[firstIdx + 1]);
                maxBeauty = Math.max(maxBeauty, beauty);
            } else {
                firstSeen.put(val, i);
            }
        }
        
        return maxBeauty;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 26. Matrix Manipulation (HackerRank) / 2D Difference Array Template
**Đề bài chi tiết:** Cho ma trận 2D kích thước $N \times M$ gồm toàn số 0. Cho mảng các thao tác cập nhật dạng `[r1, c1, r2, c2, val]`, yêu cầu cộng `val` vào ma trận con từ góc `(r1, c1)` đến `(r2, c2)`. Sau tất cả cập nhật, tìm giá trị lớn nhất trong ma trận.
**Phân tích thuật toán:** Đây là phiên bản 2D của Array Manipulation. Ta sử dụng 2D Difference Array (Mảng chênh lệch 2 chiều). Mỗi thao tác update `[r1, c1, r2, c2]` bằng giá trị `V` sẽ cập nhật 4 điểm trong mảng chênh lệch: `diff[r1][c1] += V`, `diff[r2+1][c1] -= V`, `diff[r1][c2+1] -= V`, và bù lại `diff[r2+1][c2+1] += V`. Cuối cùng, dùng 2D Prefix Sum để phục hồi và quét tìm Max.
**Mã nguồn Java:**
```java
public class MatrixManipulation {
    public long maxMatrix(int n, int m, int[][] queries) {
        long[][] diff = new long[n + 2][m + 2];
        
        // Ghi nhận các thao tác trên mảng chênh lệch
        for (int[] q : queries) {
            int r1 = q[0], c1 = q[1];
            int r2 = q[2], c2 = q[3];
            int val = q[4];
            
            diff[r1][c1] += val;
            diff[r2 + 1][c1] -= val;
            diff[r1][c2 + 1] -= val;
            diff[r2 + 1][c2 + 1] += val;
        }
        
        long maxVal = 0;
        long[][] res = new long[n + 1][m + 1];
        
        // Phục hồi bằng 2D Prefix Sum
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                res[i][j] = diff[i][j] + res[i - 1][j] + res[i][j - 1] - res[i - 1][j - 1];
                maxVal = Math.max(maxVal, res[i][j]);
            }
        }
        
        return maxVal;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(Q + N \times M)$, Space $\mathcal{O}(N \times M)$.

---

## 27. Smallest Rotation with Highest Score (LeetCode 798)
**Đề bài chi tiết:** Cho mảng `nums`. Mỗi phép quay (rotation) dịch chuyển các phần tử sang trái. Điểm số của bạn là số lượng phần tử sao cho `nums[i] <= i`. Tìm số bước dịch chuyển $k$ (từ $0$ đến $n-1$) sao cho điểm số là lớn nhất. Nếu có nhiều $k$, trả về $k$ nhỏ nhất.
**Phân tích thuật toán:** Bất kỳ phần tử `nums[i]` nào cũng sẽ đóng góp 1 điểm nếu nó nằm ở các vị trí từ `nums[i]` đến `n-1`. Nghĩa là khi dịch chuyển đi $k$ bước, có những khoảng $k$ sẽ làm cho $nums[i] \le i_{\text{mới}}$. Phân tích sự thay đổi chỉ số, mỗi $nums[i]$ sẽ mang lại điểm số trong một khoảng $k \in [L, R]$. Bài toán quy về: Mỗi số thêm +1 vào một đoạn khoảng $k$. Tìm điểm $k$ bị đè lên nhiều nhất. Ta dùng Difference Array kích thước $N+1$.
**Mã nguồn Java:**
```java
public class BestRotation {
    public int bestRotation(int[] nums) {
        int n = nums.length;
        int[] diff = new int[n + 1];
        
        for (int i = 0; i < n; i++) {
            // Xác định khoảng quay K hợp lệ
            // K nằm trong đoạn: [(i + 1) % n, (i - nums[i] + n + 1) % n - 1]
            int left = (i + 1) % n;
            int right = (i - nums[i] + n + 1) % n;
            
            diff[left]++;
            diff[right]--;
            
            // Nếu đoạn bị chia cắt (wrap around), ta phải tăng gốc 0
            if (left >= right) {
                diff[0]++;
            }
        }
        
        int bestK = 0;
        int maxScore = -1;
        int currentScore = 0;
        
        for (int k = 0; k < n; k++) {
            currentScore += diff[k];
            if (currentScore > maxScore) {
                maxScore = currentScore;
                bestK = k;
            }
        }
        return bestK;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 28. Find the Maximum Number of Marked Indices (LeetCode 2576)
**Đề bài chi tiết:** Cho mảng số nguyên `nums`. Ban đầu chưa có chỉ số nào được đánh dấu. Bạn có thể chọn hai chỉ số $i, j$ (chưa được đánh dấu) sao cho $2 \times nums[i] \le nums[j]$ và đánh dấu cả hai. Tìm số lượng chỉ số được đánh dấu tối đa.
**Phân tích thuật toán:** Sắp xếp mảng. Để có nhiều cặp nhất, ta chia mảng làm 2 nửa. Nửa trái từ `0` đến `n/2 - 1` sẽ được ghép với nửa phải từ `n/2` đến `n - 1`. Sử dụng con trỏ Two Pointers. Dù bài này thiên về Greedy, nhưng tư tưởng "đánh dấu vùng" có liên kết mật thiết với việc chia khoảng (Interval Range logic) giống như Mảng chênh lệch trong phân tích dãy.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class MaxMarkedIndices {
    public int maxNumOfMarkedIndices(int[] nums) {
        Arrays.sort(nums);
        int n = nums.length;
        int i = 0; // Con trỏ nửa trái
        int j = n / 2; // Con trỏ nửa phải
        int count = 0;
        
        while (i < n / 2 && j < n) {
            if (2 * nums[i] <= nums[j]) {
                count += 2;
                i++;
                j++;
            } else {
                j++;
            }
        }
        
        return count;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log N)$ (do sắp xếp), Space $\mathcal{O}(1)$.

---

## 29. Count Subarrays Where Max Element Appears at Least K Times (LeetCode 2962)
**Đề bài chi tiết:** Cho mảng `nums` và số nguyên `k`. Tìm tổng số lượng mảng con liên tiếp sao cho phần tử lớn nhất của toàn bộ mảng `nums` xuất hiện ít nhất `k` lần trong mảng con đó.
**Phân tích thuật toán:** Thay vì cập nhật khoảng như Difference Array, ta dùng Sliding Window để đếm chênh lệch. Đầu tiên tìm `MAX_VAL`. Duyệt con trỏ phải `R`, nếu gặp `MAX_VAL` thì tăng bộ đếm. Khi bộ đếm đạt `k`, ta dịch con trỏ trái `L` lên đến khi bộ đếm giảm xuống dưới `k`. Mọi vị trí kết thúc `R` sẽ có đúng `L` mảng con hợp lệ (bắt đầu từ $0$ đến $L-1$).
**Mã nguồn Java:**
```java
public class CountSubarraysMaxK {
    public long countSubarrays(int[] nums, int k) {
        int maxElement = 0;
        for (int num : nums) {
            maxElement = Math.max(maxElement, num);
        }
        
        long ans = 0;
        int count = 0;
        int left = 0;
        
        for (int right = 0; right < nums.length; right++) {
            if (nums[right] == maxElement) {
                count++;
            }
            
            while (count >= k) {
                if (nums[left] == maxElement) {
                    count--;
                }
                left++;
            }
            
            // Số lượng mảng con hợp lệ kết thúc tại `right` chính là `left`
            ans += left;
        }
        
        return ans;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 30. Continuous Subarrays (LeetCode 2762) - Subarray Range Difference
**Đề bài chi tiết:** Cho mảng số nguyên `nums`. Một mảng con được gọi là "liên tục" (continuous) nếu độ lệch tuyệt đối giữa 2 phần tử bất kỳ trong mảng con đó không vượt quá 2. Đếm tổng số lượng mảng con liên tục. (Liên hệ: Minimum/Maximum Differences Array).
**Phân tích thuật toán:** Khoảng cách tối đa $\le 2$ có nghĩa là $Max - Min \le 2$. Dùng `TreeMap` để duy trì giá trị lớn nhất và nhỏ nhất của cửa sổ trượt (Sliding Window). Khi $Max - Min > 2$, ta co cửa sổ lại bằng cách gỡ bỏ phần tử `nums[L]` khỏi TreeMap cho đến khi thỏa mãn. Số lượng mảng con kết thúc tại `R` là `R - L + 1`.
**Mã nguồn Java:**
```java
import java.util.TreeMap;

public class ContinuousSubarrays {
    public long continuousSubarrays(int[] nums) {
        TreeMap<Integer, Integer> map = new TreeMap<>();
        long totalSubarrays = 0;
        int left = 0;
        
        for (int right = 0; right < nums.length; right++) {
            map.put(nums[right], map.getOrDefault(nums[right], 0) + 1);
            
            while (map.lastKey() - map.firstKey() > 2) {
                int leftVal = nums[left];
                map.put(leftVal, map.get(leftVal) - 1);
                if (map.get(leftVal) == 0) {
                    map.remove(leftVal);
                }
                left++;
            }
            
            totalSubarrays += (right - left + 1);
        }
        
        return totalSubarrays;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log K)$ với $K \le 3$ (kích thước map rất nhỏ), tương đương $\mathcal{O}(N)$. Space $\mathcal{O}(1)$ do `TreeMap` chỉ lưu tối đa 3 khóa.


