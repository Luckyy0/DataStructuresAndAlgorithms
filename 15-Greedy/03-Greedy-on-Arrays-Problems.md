# Bài tập Greedy on Arrays (Tham lam trên mảng)

Danh sách 30 bài tập bao phủ các kỹ thuật của thuật toán tham lam trên mảng. Dưới đây là 10 bài toán chi tiết với phân tích và mã nguồn, và 20 bài tập tóm tắt ở phần sau.

## 1. Jump Game (LeetCode 55)
**Đề bài chi tiết:** Cho một mảng số nguyên không âm `nums`. Ban đầu bạn ở vị trí số `0`. Mỗi phần tử biểu thị khoảng cách nhảy tối đa (maximum jump length). Hãy xác định xem bạn có thể nhảy đến phần tử cuối cùng hay không.
**Phân tích thuật toán:**
Duyệt mảng một chiều từ trái sang phải, sử dụng biến `reachable` để duy trì chỉ số xa nhất có thể đạt được. Nếu tại bước `i`, `i > reachable` thì ta đã kẹt tại đây. Ngược lại, cập nhật `reachable = max(reachable, i + nums[i])`.
**Mã nguồn Java:**
```java
public class JumpGame {
    public boolean canJump(int[] nums) {
        int reachable = 0;
        for (int i = 0; i < nums.length; i++) {
            if (i > reachable) return false;
            reachable = Math.max(reachable, i + nums[i]);
            if (reachable >= nums.length - 1) return true;
        }
        return true;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N)$ vì duyệt tuyến tính 1 lần.
- Space Complexity: $O(1)$ không dùng mảng phụ.

## 2. Jump Game II (LeetCode 45)
**Đề bài chi tiết:** Tương tự như Jump Game, nhưng dữ liệu luôn đảm bảo bạn đến được vị trí cuối cùng. Tính số lần nhảy tối thiểu (minimum number of jumps) để đến đích.
**Phân tích thuật toán:**
Theo dõi trạng thái kết thúc của bước nhảy hiện tại (`currentEnd`) và khoảng cách xa nhất tiếp theo (`farthest`). Duyệt mảng, liên tục cập nhật `farthest`. Khi vị trí `i` chạm tới `currentEnd`, ta bắt buộc phải nhảy để mở rộng vùng, cập nhật số lần nhảy `jumps++` và gán `currentEnd = farthest`.
**Mã nguồn Java:**
```java
public class JumpGameII {
    public int jump(int[] nums) {
        int jumps = 0, currentEnd = 0, farthest = 0;
        for (int i = 0; i < nums.length - 1; i++) {
            farthest = Math.max(farthest, i + nums[i]);
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
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$

## 3. Gas Station (LeetCode 134)
**Đề bài chi tiết:** Có `N` trạm xăng trên một vòng tròn. Trạm `i` có lượng xăng `gas[i]` và tốn `cost[i]` để đến trạm tiếp theo `i+1`. Tìm chỉ số xuất phát (starting index) để chạy đúng 1 vòng tròn, hoặc `-1` nếu bất thi.
**Phân tích thuật toán:**
Kiểm tra tổng trước: Nếu tổng `gas < total cost`, không thể hoàn thành. Ta dùng biến `currentTank` tích lũy lượng xăng, nếu `currentTank < 0`, mọi trạm trước đó đều không thể làm trạm xuất phát, ta đổi trạm xuất phát sang `i + 1` và reset xăng về `0`.
**Mã nguồn Java:**
```java
public class GasStation {
    public int canCompleteCircuit(int[] gas, int[] cost) {
        int totalTank = 0, currentTank = 0, startingStation = 0;
        for (int i = 0; i < gas.length; i++) {
            int net = gas[i] - cost[i];
            totalTank += net;
            currentTank += net;
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
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$

## 4. Candy (LeetCode 135)
**Đề bài chi tiết:** `N` đứa trẻ xếp thành hàng ngang với các điểm đánh giá (ratings). Phân phát kẹo với điều kiện: Mỗi trẻ có tối thiểu 1 viên kẹo. Đứa trẻ có rating cao hơn láng giềng thì phải có nhiều kẹo hơn láng giềng đó. Tìm số kẹo tối thiểu.
**Phân tích thuật toán:**
Dùng kỹ thuật Duyệt hai chiều (Two Passes). Lượt 1 từ trái qua phải, nếu rating lớn hơn bé bên trái, thưởng +1 viên kẹo. Lượt 2 đi từ phải qua trái, làm điều tương tự đối với bé bên phải, dùng hàm `max()` để hợp nhất kết quả nhằm thỏa mãn cả hai điều kiện cùng lúc.
**Mã nguồn Java:**
```java
import java.util.Arrays;
public class Candy {
    public int candy(int[] ratings) {
        int n = ratings.length;
        int[] candies = new int[n];
        Arrays.fill(candies, 1);
        for (int i = 1; i < n; i++) {
            if (ratings[i] > ratings[i - 1]) candies[i] = candies[i - 1] + 1;
        }
        int total = candies[n - 1];
        for (int i = n - 2; i >= 0; i--) {
            if (ratings[i] > ratings[i + 1]) candies[i] = Math.max(candies[i], candies[i + 1] + 1);
            total += candies[i];
        }
        return total;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(N)$ (cần mảng phụ `candies`).

## 5. Can Place Flowers (LeetCode 605)
**Đề bài chi tiết:** Cho mảng `flowerbed` chứa các số 0 (trống) và 1 (có hoa). Hoa không được trồng cạnh nhau. Kiểm tra xem có thể trồng thêm `n` bông hoa không.
**Phân tích thuật toán:**
Cứ gặp vị trí trống thỏa mãn điều kiện, ta trồng ngay tức khắc (Tham lam cục bộ). Điều kiện trống là bản thân vị trí đó là 0, và hai bên cạnh cũng phải là 0 hoặc lề (edge).
**Mã nguồn Java:**
```java
public class CanPlaceFlowers {
    public boolean canPlaceFlowers(int[] flowerbed, int n) {
        int count = 0;
        for (int i = 0; i < flowerbed.length; i++) {
            if (flowerbed[i] == 0) {
                boolean emptyLeft = (i == 0) || (flowerbed[i - 1] == 0);
                boolean emptyRight = (i == flowerbed.length - 1) || (flowerbed[i + 1] == 0);
                if (emptyLeft && emptyRight) {
                    flowerbed[i] = 1;
                    count++;
                }
            }
        }
        return count >= n;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$

## 6. Best Time to Buy and Sell Stock II (LeetCode 122)
**Đề bài chi tiết:** Cho mảng `prices` với giá cổ phiếu từng ngày. Bạn có thể mua và bán bao nhiêu lần tùy thích nhưng mỗi thời điểm chỉ giữ tối đa 1 cổ phiếu. Tính max profit.
**Phân tích thuật toán:**
Accumulating differences (Tích lũy các chênh lệch dương). Bất cứ lúc nào giá ngày hôm sau lớn hơn ngày hôm trước, ta "mặc định" mua và bán ngay để cộng khoản lợi nhuận này vào tổng.
**Mã nguồn Java:**
```java
public class StockBuySellII {
    public int maxProfit(int[] prices) {
        int profit = 0;
        for (int i = 1; i < prices.length; i++) {
            if (prices[i] > prices[i - 1]) {
                profit += prices[i] - prices[i - 1];
            }
        }
        return profit;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$

## 7. Maximum Subarray (Kadane's Algorithm) (LeetCode 53)
**Đề bài chi tiết:** Tìm mảng con liên tiếp (contiguous subarray) có tổng các phần tử lớn nhất và trả về tổng đó.
**Phân tích thuật toán:**
Thuật toán Kadane có bản chất là Greedy (có yếu tố DP). Nếu một đoạn mảng đang theo dõi bị rớt xuống âm, nó sẽ làm "gánh nặng" cho các phần tử dương phía sau, do đó ta bỏ đoạn âm đi và bắt đầu tổng lại từ số hiện tại `Math.max(nums[i], currentSum + nums[i])`.
**Mã nguồn Java:**
```java
public class MaxSubArray {
    public int maxSubArray(int[] nums) {
        int maxSum = nums[0], currentSum = nums[0];
        for (int i = 1; i < nums.length; i++) {
            currentSum = Math.max(nums[i], currentSum + nums[i]);
            maxSum = Math.max(maxSum, currentSum);
        }
        return maxSum;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$

## 8. Minimum Number of Taps to Open to Water a Garden (LeetCode 1326)
**Đề bài chi tiết:** Có một khu vườn từ `0` đến `n`. Có `n + 1` vòi nước tại mỗi điểm. Vòi thứ `i` tưới được từ `i - ranges[i]` đến `i + ranges[i]`. Tìm số vòi ít nhất để tưới kín.
**Phân tích thuật toán:**
Chuyển bài toán thành "Jump Game II". Thay vì lưu ranges, ta tạo mảng `maxReach` lưu vị trí vươn xa nhất từ mọi chỉ số `start`. Sau đó thực hiện Greedy để đếm số bước nhảy (tương đương số vòi cần mở).
**Mã nguồn Java:**
```java
public class MinimumTaps {
    public int minTaps(int n, int[] ranges) {
        int[] maxReach = new int[n + 1];
        for (int i = 0; i < ranges.length; i++) {
            if (ranges[i] == 0) continue;
            int left = Math.max(0, i - ranges[i]);
            int right = Math.min(n, i + ranges[i]);
            maxReach[left] = Math.max(maxReach[left], right);
        }
        int taps = 0, currentEnd = 0, farthest = 0;
        for (int i = 0; i < n; i++) {
            farthest = Math.max(farthest, maxReach[i]);
            if (i == currentEnd) {
                if (farthest <= i) return -1;
                taps++;
                currentEnd = farthest;
            }
        }
        return taps;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(N)$ (Mảng trung gian `maxReach`).

## 9. Minimum Moves to Equal Array Elements II (LeetCode 462)
**Đề bài chi tiết:** Cho mảng `nums`. Tính số bước tối thiểu (+1 hoặc -1) để biến tất cả các số trong mảng thành một số giống nhau.
**Phân tích thuật toán:**
Tính tối ưu dựa trên điểm trung vị (Median), chứ không phải trung bình cộng (Mean). Sắp xếp mảng để tìm median, sau đó tham lam tính khoảng cách tuyệt đối từ mỗi phần tử đến trung vị này.
**Mã nguồn Java:**
```java
import java.util.Arrays;
public class MinMovesII {
    public int minMoves2(int[] nums) {
        Arrays.sort(nums);
        int median = nums[nums.length / 2];
        int moves = 0;
        for (int num : nums) {
            moves += Math.abs(num - median);
        }
        return moves;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N \log N)$ (do Sorting).
- Space Complexity: $O(1)$ (Không tính bộ nhớ ngầm của hệ thống Sort).

## 10. Container With Most Water (LeetCode 11)
**Đề bài chi tiết:** Cho mảng độ cao của các bức tường `height`. Chọn hai bức tường bất kỳ để tạo thành 1 bể chứa nước nhiều nhất.
**Phân tích thuật toán:**
Dùng Greedy + Two Pointers. Để tối đa hóa nước, ta ưu tiên bề rộng xa nhất bằng cách xuất phát từ đầu và cuối. Thể tích nước bị giới hạn bởi vách thấp hơn. Nếu di chuyển vách cao, thể tích không thể tăng. Ta phải di chuyển vách thấp hơn vào trong để hy vọng lấy được vách cao hơn khác.
**Mã nguồn Java:**
```java
public class ContainerWithMostWater {
    public int maxArea(int[] height) {
        int maxArea = 0;
        int left = 0, right = height.length - 1;
        while (left < right) {
            int currentArea = Math.min(height[left], height[right]) * (right - left);
            maxArea = Math.max(maxArea, currentArea);
            if (height[left] < height[right]) left++;
            else right--;
        }
        return maxArea;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$

---

## 11. Assign Cookies (LeetCode 455)
**Đề bài chi tiết:** Cho hai mảng `g` (độ tham lam của mỗi đứa trẻ) và `s` (kích thước của từng cái bánh quy). Mỗi đứa trẻ `i` chỉ cảm thấy thỏa mãn nếu nhận được cái bánh quy `j` có kích thước `s[j] >= g[i]`. Bạn chỉ được phát nhiều nhất 1 cái bánh quy cho 1 đứa trẻ. Tìm số lượng trẻ lớn nhất có thể được thỏa mãn.
**Phân tích thuật toán:**
Để tối đa hóa số lượng trẻ được nhận bánh, ta nên ưu tiên thỏa mãn những đứa trẻ có độ tham lam ít nhất bằng những chiếc bánh quy nhỏ nhất mà vẫn vừa đủ làm chúng vui. Sắp xếp cả 2 mảng `g` và `s`. Dùng hai con trỏ duyệt qua hai mảng. Nếu `s[j] >= g[i]`, đứa trẻ `i` được thỏa mãn, ta tăng cả 2 con trỏ. Nếu không, bánh quy quá nhỏ, ta chỉ tăng con trỏ bánh quy `j`.
**Mã nguồn Java:**
```java
import java.util.Arrays;
public class AssignCookies {
    public int findContentChildren(int[] g, int[] s) {
        Arrays.sort(g);
        Arrays.sort(s);
        int i = 0, j = 0;
        while (i < g.length && j < s.length) {
            if (s[j] >= g[i]) {
                i++;
            }
            j++;
        }
        return i;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N \log N + M \log M)$ với $N, M$ là chiều dài mảng do thời gian sắp xếp.
- Space Complexity: $O(1)$ hoặc ngầm định $O(\log N)$ tùy thuộc thuật toán sắp xếp của Java.

## 12. Lemonade Change (LeetCode 860)
**Đề bài chi tiết:** Tại một quầy bán nước chanh, mỗi ly giá 5 đô la. Khách hàng xếp hàng để mua, và mỗi người sẽ đưa đúng 1 tờ 5, 10, hoặc 20 đô la. Quầy ban đầu không có tiền lẻ. Bạn phải trả lại tiền thối chính xác cho mỗi khách. Trả về `true` nếu có thể trả thối cho tất cả, ngược lại `false`.
**Phân tích thuật toán:**
Dùng chiến lược tham lam (Greedy) khi thối tiền: luôn giữ lại tờ tiền mệnh giá nhỏ nhất (5 đô la) nếu có thể, vì tờ 5 đô la có tính linh hoạt cao nhất (có thể thối cho khách đưa 10 đô và 20 đô). Khi khách đưa 20 đô, ta ưu tiên thối lại 1 tờ 10 đô và 1 tờ 5 đô; nếu không có tờ 10 đô, đành thối lại 3 tờ 5 đô.
**Mã nguồn Java:**
```java
public class LemonadeChange {
    public boolean lemonadeChange(int[] bills) {
        int five = 0, ten = 0;
        for (int bill : bills) {
            if (bill == 5) {
                five++;
            } else if (bill == 10) {
                if (five == 0) return false;
                five--;
                ten++;
            } else {
                if (five > 0 && ten > 0) {
                    five--;
                    ten--;
                } else if (five >= 3) {
                    five -= 3;
                } else {
                    return false;
                }
            }
        }
        return true;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N)$ duyêt qua mảng `bills` một lần.
- Space Complexity: $O(1)$ chỉ sử dụng 2 biến đếm.

## 13. Wiggle Subsequence (LeetCode 376)
**Đề bài chi tiết:** Một wiggle sequence là dãy số mà sự khác biệt giữa các phần tử liên tiếp luôn luân phiên dương và âm. Cho một mảng `nums`, trả về chiều dài lớn nhất của một mảng con không liên tiếp (subsequence) có tính chất wiggle.
**Phân tích thuật toán:**
Dãy dài nhất đạt được bằng cách chỉ lấy các điểm "cực trị" (các đỉnh cao nhất và đáy thấp nhất của đồ thị đường gấp khúc). Khi giá trị tăng, ta đếm một đỉnh trên (`up = down + 1`). Khi giá trị giảm, ta đếm một đỉnh dưới (`down = up + 1`). Những điểm nằm trên dốc (đang tăng liên tiếp hoặc giảm liên tiếp) sẽ bị bỏ qua bằng cách không cập nhật biến đếm.
**Mã nguồn Java:**
```java
public class WiggleSubsequence {
    public int wiggleMaxLength(int[] nums) {
        if (nums.length < 2) return nums.length;
        int up = 1, down = 1;
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] > nums[i - 1]) {
                up = down + 1;
            } else if (nums[i] < nums[i - 1]) {
                down = up + 1;
            }
        }
        return Math.max(up, down);
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N)$ chỉ duyệt mảng một lần.
- Space Complexity: $O(1)$ không lưu trữ thêm dữ liệu phụ.

## 14. Non-overlapping Intervals (LeetCode 435)
**Đề bài chi tiết:** Cho một mảng các khoảng thời gian `intervals`, trong đó `intervals[i] = [start_i, end_i]`. Hãy trả về số lượng khoảng ít nhất cần loại bỏ để các khoảng còn lại không bị chồng lấn (non-overlapping) lên nhau.
**Phân tích thuật toán:**
Sắp xếp mảng theo thời gian kết thúc (`End Time`) tăng dần. Ý tưởng tham lam ở đây là: luôn ưu tiên giữ lại các khoảng có thời gian kết thúc sớm nhất. Điều này để dành khoảng thời gian dài nhất về phía sau cho các công việc khác. Nếu một khoảng mới bắt đầu trước hoặc trùng lúc khoảng hiện tại đang chạy, nó bị chồng lấn và ta phải đếm +1 số khoảng bị loại.
**Mã nguồn Java:**
```java
import java.util.Arrays;
public class NonOverlappingIntervals {
    public int eraseOverlapIntervals(int[][] intervals) {
        if (intervals.length == 0) return 0;
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[1], b[1]));
        int count = 0;
        int currentEnd = intervals[0][1];
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] < currentEnd) {
                count++;
            } else {
                currentEnd = intervals[i][1];
            }
        }
        return count;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N \log N)$ do thao tác sắp xếp các khoảng thời gian.
- Space Complexity: $O(1)$ hoặc $O(\log N)$ tùy thuộc thuật toán sort.

## 15. Array Partition I (LeetCode 561)
**Đề bài chi tiết:** Cho một mảng số nguyên `nums` có `2n` phần tử. Hãy nhóm chúng thành `n` cặp `(a1, b1), (a2, b2), ..., (an, bn)` sao cho tổng của hàm `min(ai, bi)` đối với mọi cặp là lớn nhất có thể.
**Phân tích thuật toán:**
Để hàm `min(a, b)` không bị thiệt thòi nhiều (nghĩa là không phải chọn một số rất nhỏ so với số còn lại), ta nên ghép cặp các con số có giá trị gần nhau nhất. Thuật toán tham lam ở đây đơn giản là sắp xếp mảng tăng dần, sau đó ghép cặp 2 phần tử kề nhau, lấy phần tử nhỏ hơn (đứng ở vị trí chẵn `0, 2, 4, ...`).
**Mã nguồn Java:**
```java
import java.util.Arrays;
public class ArrayPartition {
    public int arrayPairSum(int[] nums) {
        Arrays.sort(nums);
        int sum = 0;
        for (int i = 0; i < nums.length; i += 2) {
            sum += nums[i];
        }
        return sum;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N \log N)$ vì thuật toán cần sắp xếp mảng.
- Space Complexity: $O(1)$ hoặc ngầm $O(\log N)$ khi sử dụng hàm sort.

## 16. Task Scheduler (LeetCode 621)
**Đề bài chi tiết:** Bạn có một mảng ký tự `tasks` đại diện cho các công việc, và số nguyên `n` đại diện cho thời gian giãn cách tối thiểu (cooldown) giữa 2 công việc giống nhau (cùng ký tự). Tính thời gian ít nhất cần để hoàn thành mọi công việc. Mỗi đơn vị thời gian chạy 1 công việc, hoặc nghỉ rảnh (`idle`).
**Phân tích thuật toán:**
Để giảm bớt khoảng thời gian rảnh rỗi (`idle`), ta nên phân bổ công việc có số lượng nhiều nhất trước để định hình bộ khung. Nếu số lượng max là `maxFreq`, công việc này sẽ cắt thời gian thành `(maxFreq - 1)` khối giãn cách. Kích thước mỗi khối là `n`. Ta nhét các task nhỏ hơn vào trong những khối trống (slots) này. Cuối cùng tính toán tổng slots rảnh nếu chưa được lấp đầy.
**Mã nguồn Java:**
```java
import java.util.Arrays;
public class TaskScheduler {
    public int leastInterval(char[] tasks, int n) {
        int[] frequencies = new int[26];
        for (char t : tasks) {
            frequencies[t - 'A']++;
        }
        Arrays.sort(frequencies);
        int maxFreq = frequencies[25];
        int idleSlots = (maxFreq - 1) * n;
        
        for (int i = 24; i >= 0 && frequencies[i] > 0; i--) {
            idleSlots -= Math.min(maxFreq - 1, frequencies[i]);
        }
        return idleSlots > 0 ? idleSlots + tasks.length : tasks.length;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(T)$ với $T$ là tổng số task. Vòng lặp sắp xếp đếm phần tử mảng tốn $O(1)$ do cố định 26 chữ cái.
- Space Complexity: $O(1)$ vì kích thước mảng tần suất cố định ở 26.

## 17. Partition Labels (LeetCode 763)
**Đề bài chi tiết:** Bạn có một chuỗi `s` gồm các ký tự tiếng Anh in thường. Cần chia chuỗi thành nhiều phần nhất có thể sao cho không có ký tự nào xuất hiện ở 2 phần khác nhau. Trả về danh sách chiều dài của các phần đó.
**Phân tích thuật toán:**
Dùng mảng `last` lưu trữ vị trí xuất hiện cuối cùng của mọi ký tự. Duyệt chuỗi một lần nữa bằng 2 con trỏ, duy trì `end = Math.max(end, last[s.charAt(i) - 'a'])`. Nếu con trỏ hiện tại `i` trùng với `end`, tức là tất cả ký tự gặp từ đầu đoạn tới giờ đều đã "chốt" trọn vẹn ở bên trong đoạn hiện tại. Ta ghi nhận kích thước đoạn này và cắt.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;
public class PartitionLabels {
    public List<Integer> partitionLabels(String s) {
        int[] last = new int[26];
        for (int i = 0; i < s.length(); i++) {
            last[s.charAt(i) - 'a'] = i;
        }
        
        List<Integer> result = new ArrayList<>();
        int start = 0, end = 0;
        for (int i = 0; i < s.length(); i++) {
            end = Math.max(end, last[s.charAt(i) - 'a']);
            if (i == end) {
                result.add(end - start + 1);
                start = i + 1;
            }
        }
        return result;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N)$ với $N$ là chiều dài chuỗi.
- Space Complexity: $O(1)$ vì bảng hash có độ dài không đổi 26 ký tự.

## 18. Two City Scheduling (LeetCode 1029)
**Đề bài chi tiết:** Một công ty muốn phỏng vấn `2n` ứng viên, được cung cấp mảng `costs` trong đó `costs[i] = [aCost_i, bCost_i]` là chi phí bay tới thành phố A và thành phố B. Hãy điều động chính xác `n` người tới A và `n` người tới B sao cho tổng chi phí là nhỏ nhất.
**Phân tích thuật toán:**
Sử dụng khái niệm "chi phí cơ hội" (opportunity cost). Nếu gửi ứng viên `i` tới thành phố A thay vì B, ta "lời" (hoặc "lỗ") một lượng là `costA - costB`. Sắp xếp mảng ứng viên theo giá trị chênh lệch này tăng dần. Khi đó, `n` người đầu tiên sẽ là những người tạo ra lợi ích lớn nhất (âm nhiều nhất) nếu đi thành phố A, và `n` người cuối cùng sẽ là đi thành phố B rẻ hơn.
**Mã nguồn Java:**
```java
import java.util.Arrays;
public class TwoCityScheduling {
    public int twoCitySchedCost(int[][] costs) {
        Arrays.sort(costs, (a, b) -> Integer.compare(a[0] - a[1], b[0] - b[1]));
        
        int totalCost = 0;
        int n = costs.length / 2;
        for (int i = 0; i < n; i++) {
            totalCost += costs[i][0];
        }
        for (int i = n; i < costs.length; i++) {
            totalCost += costs[i][1];
        }
        return totalCost;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N \log N)$ dùng cho việc sắp xếp.
- Space Complexity: $O(1)$.

## 19. Advantage Shuffle (LeetCode 870)
**Đề bài chi tiết:** Cho hai mảng `nums1` và `nums2` cùng độ dài. Lợi thế (advantage) của `nums1` so với `nums2` là số chỉ số `i` mà `nums1[i] > nums2[i]`. Thay đổi hoán vị của `nums1` để tối đa hóa lợi thế.
**Phân tích thuật toán:**
Sử dụng chiến lược "Ngựa đua Tề Vương". Sắp xếp `nums1` và danh sách các cặp (giá trị, chỉ số) của `nums2`. Ta duyệt `nums2` theo giá trị giảm dần (con ngựa lớn nhất của địch). Nếu số lớn nhất trong `nums1` hiện tại mạnh hơn, ta dùng nó để đánh bại. Nếu nó không mạnh bằng, chắc chắn mọi số khác trong `nums1` cũng vậy; lúc này ta hy sinh con nhỏ nhất của `nums1` cho cuộc chiến không cân sức này.
**Mã nguồn Java:**
```java
import java.util.Arrays;
import java.util.PriorityQueue;
public class AdvantageShuffle {
    public int[] advantageCount(int[] nums1, int[] nums2) {
        Arrays.sort(nums1);
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) -> Integer.compare(b[0], a[0]));
        for (int i = 0; i < nums2.length; i++) {
            maxHeap.offer(new int[]{nums2[i], i});
        }
        
        int[] result = new int[nums1.length];
        int left = 0, right = nums1.length - 1;
        
        while (!maxHeap.isEmpty()) {
            int[] current = maxHeap.poll();
            int val = current[0], idx = current[1];
            if (nums1[right] > val) {
                result[idx] = nums1[right];
                right--;
            } else {
                result[idx] = nums1[left];
                left++;
            }
        }
        return result;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N \log N)$ để sắp xếp `nums1` và đẩy vào cấu trúc PriorityQueue.
- Space Complexity: $O(N)$ dùng lưu trữ Queue và mảng kết quả.

## 20. Reorganize String (LeetCode 767)
**Đề bài chi tiết:** Cho một chuỗi `s`, hãy sắp xếp lại các ký tự trong chuỗi sao cho bất kỳ hai ký tự kề nhau nào cũng không giống nhau. Trả về chuỗi kết quả (hoặc chuỗi rỗng nếu không thể).
**Phân tích thuật toán:**
Đếm số lần xuất hiện của các ký tự. Nếu ký tự xuất hiện nhiều nhất vượt qua `(len + 1) / 2`, thì chắc chắn bất khả thi. Cách tham lam: Xếp ký tự phổ biến nhất cách nhau một ô (vào các chỉ số chẵn 0, 2, 4...). Sau khi xếp xong ký tự đó, các ký tự còn lại cứ tiếp tục điền luân phiên (hết chỉ số chẵn thì vòng lại điền chỉ số lẻ 1, 3, 5...).
**Mã nguồn Java:**
```java
public class ReorganizeString {
    public String reorganizeString(String s) {
        int[] hash = new int[26];
        for (char c : s.toCharArray()) hash[c - 'a']++;
        
        int max = 0, letter = 0;
        for (int i = 0; i < hash.length; i++) {
            if (hash[i] > max) {
                max = hash[i];
                letter = i;
            }
        }
        
        if (max > (s.length() + 1) / 2) return "";
        
        char[] res = new char[s.length()];
        int idx = 0;
        // Điền ký tự xuất hiện nhiều nhất trước ở vị trí chẵn
        while (hash[letter] > 0) {
            res[idx] = (char) (letter + 'a');
            idx += 2;
            hash[letter]--;
        }
        
        // Lấp đầy các phần tử còn lại
        for (int i = 0; i < hash.length; i++) {
            while (hash[i] > 0) {
                if (idx >= res.length) {
                    idx = 1; // Vòng lại các index lẻ
                }
                res[idx] = (char) (i + 'a');
                idx += 2;
                hash[i]--;
            }
        }
        return String.valueOf(res);
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N)$ với $N$ là độ dài chuỗi do mảng đếm tần suất có 26 ký tự cố định.
- Space Complexity: $O(N)$ để phân bổ mảng kết quả char array.

---

## 21. Video Stitching (LeetCode 1024)
**Đề bài chi tiết:** Cho một loạt các đoạn video clip, mỗi clip được biểu diễn bởi một mảng `clips[i] = [start_i, end_i]`. Bạn cần cắt và nối các clip này lại để tạo thành một video liên tục bao phủ toàn bộ khoảng thời gian từ `0` đến `time`. Hãy trả về số lượng clip tối thiểu cần dùng, nếu không thể tạo thành, trả về `-1`.
**Phân tích thuật toán:**
Bài toán này tương đương với Jump Game II. Chúng ta có thể tạo một mảng `maxReach` trong đó `maxReach[i]` lưu trữ giá trị xa nhất (`end_i`) mà một clip bắt đầu từ `i` hoặc trước `i` có thể vươn tới. Sau đó, dùng tham lam để nhảy từ `0` đến `time`, luôn cố gắng nhảy xa nhất có thể. Nếu tại một bước nhảy, điểm kết thúc của nhảy trùng với chỉ số hiện tại và chưa đến đích, tức là bị kẹt, ta trả về `-1`.
**Mã nguồn Java:**
```java
public class VideoStitching {
    public int videoStitching(int[][] clips, int time) {
        int[] maxReach = new int[time + 1];
        for (int[] clip : clips) {
            int start = clip[0];
            int end = clip[1];
            if (start <= time) {
                maxReach[start] = Math.max(maxReach[start], end);
            }
        }
        
        int step = 0;
        int currentEnd = 0;
        int farthest = 0;
        
        for (int i = 0; i < time; i++) {
            farthest = Math.max(farthest, maxReach[i]);
            if (i == currentEnd) {
                if (farthest <= i) return -1;
                step++;
                currentEnd = farthest;
            }
        }
        return step;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N + T)$ trong đó $N$ là số lượng clip và $T$ là `time`.
- Space Complexity: $O(T)$ để lưu trữ mảng `maxReach`.

## 22. Bag of Tokens (LeetCode 948)
**Đề bài chi tiết:** Bạn có một số lượng `power` ban đầu và một điểm số (score) là 0, cùng với một mảng các `tokens`. Mỗi token có thể được sử dụng theo một trong hai cách: Face-up (nếu `power >= tokens[i]`, mất `tokens[i]` power và được 1 score) hoặc Face-down (nếu `score >= 1`, mất 1 score và nhận được `tokens[i]` power). Tìm điểm số lớn nhất có thể đạt được.
**Phân tích thuật toán:**
Sử dụng tham lam và hai con trỏ. Sắp xếp mảng token tăng dần. Ý tưởng tốt nhất là: khi cần lấy điểm (score), hãy tiêu tốn ít power nhất (chơi úp các token nhỏ ở đầu mảng). Khi hết power và cần nạp lại, hãy hy sinh điểm để lấy lượng power lớn nhất có thể (chơi lật các token lớn ở cuối mảng).
**Mã nguồn Java:**
```java
import java.util.Arrays;
public class BagOfTokens {
    public int bagOfTokensScore(int[] tokens, int power) {
        Arrays.sort(tokens);
        int left = 0, right = tokens.length - 1;
        int score = 0, maxScore = 0;
        
        while (left <= right) {
            if (power >= tokens[left]) {
                power -= tokens[left++];
                score++;
                maxScore = Math.max(maxScore, score);
            } else if (score > 0) {
                power += tokens[right--];
                score--;
            } else {
                break;
            }
        }
        return maxScore;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N \log N)$ do thao tác sắp xếp mảng `tokens`.
- Space Complexity: $O(1)$ chỉ dùng hai con trỏ.

## 23. Broken Calculator (LeetCode 991)
**Đề bài chi tiết:** Có một máy tính bị hỏng, trên màn hình hiện số `startValue`. Bạn chỉ có thể thực hiện 2 thao tác: nhân số hiện tại với 2, hoặc trừ số hiện tại đi 1. Tìm số thao tác tối thiểu để biến `startValue` thành `target`.
**Phân tích thuật toán:**
Duyệt từ `startValue` đến `target` sẽ rất phức tạp để quyết định khi nào nên nhân, khi nào trừ. Thay vào đó, ta đi ngược lại từ `target` về `startValue`. Nếu `target > startValue`: nếu `target` chẵn, ta chắc chắn nên chia 2 (vì chi phí ngược lại là nhân 2, rất hiệu quả). Nếu `target` lẻ, ta cộng thêm 1 để nó chẵn. Khi `target <= startValue`, ta chỉ có một cách là cộng dần 1 vào `target` (tương đương trừ dần 1 từ `startValue`).
**Mã nguồn Java:**
```java
public class BrokenCalculator {
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
        return operations + (startValue - target);
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(\log(\text{target}))$ vì mỗi lần chia đôi giúp giảm đáng kể giá trị của đích.
- Space Complexity: $O(1)$.

## 24. Boats to Save People (LeetCode 881)
**Đề bài chi tiết:** Cho mảng `people` trong đó `people[i]` là cân nặng của người thứ `i`, và một số nguyên `limit` đại diện cho tải trọng tối đa của một chiếc thuyền. Mỗi thuyền chỉ chở tối đa 2 người cùng lúc và tổng cân nặng không vượt quá `limit`. Tìm số thuyền tối thiểu để chở hết mọi người.
**Phân tích thuật toán:**
Tham lam bằng hai con trỏ. Sắp xếp cân nặng của mọi người. Để tiết kiệm thuyền, ta nên ghép đôi người nặng nhất với người nhẹ nhất có thể. Khởi tạo con trỏ `left` ở người nhẹ nhất và `right` ở người nặng nhất. Nếu `people[left] + people[right] <= limit`, hai người này cùng đi một thuyền (tăng `left`, giảm `right`). Nếu vượt tải trọng, người nặng nhất bắt buộc phải đi một thuyền riêng (chỉ giảm `right`).
**Mã nguồn Java:**
```java
import java.util.Arrays;
public class BoatsToSavePeople {
    public int numRescueBoats(int[] people, int limit) {
        Arrays.sort(people);
        int left = 0, right = people.length - 1;
        int boats = 0;
        
        while (left <= right) {
            if (people[left] + people[right] <= limit) {
                left++;
            }
            // Người nặng luôn chiếm 1 vị trí trên thuyền
            right--;
            boats++;
        }
        return boats;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N \log N)$ do sắp xếp mảng.
- Space Complexity: $O(1)$ chỉ dùng con trỏ và biến đếm.

## 25. Minimum Number of Arrows to Burst Balloons (LeetCode 452)
**Đề bài chi tiết:** Có một số quả bóng bay nằm trên trục X. Bạn được cung cấp mảng `points`, trong đó `points[i] = [xstart, xend]` biểu diễn biên độ ngang của quả bóng thứ `i`. Một mũi tên bắn từ trục X và bay lên trên theo trục Y có thể làm nổ bóng nếu đâm xuyên qua đoạn ngang đó. Cần tối thiểu bao nhiêu mũi tên để bắn nổ tất cả bóng.
**Phân tích thuật toán:**
Bài toán tương tự như bài Non-overlapping Intervals. Để dùng một mũi tên bắn được nhiều bóng nhất, ta sắp xếp các quả bóng theo tọa độ kết thúc (`xend`). Ta ngắm bắn mũi tên tại chính tọa độ kết thúc sớm nhất này (`currentEnd`). Mọi quả bóng bắt đầu trước hoặc ngay tại `currentEnd` đều sẽ bị nổ lây. Khi gặp quả bóng bắt đầu sau `currentEnd`, ta bắt buộc phải tốn một mũi tên mới và cập nhật lại mốc `currentEnd`.
**Mã nguồn Java:**
```java
import java.util.Arrays;
public class MinimumArrows {
    public int findMinArrowShots(int[][] points) {
        if (points.length == 0) return 0;
        // Chú ý dùng Integer.compare để tránh tràn số học (Integer Overflow)
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
- Time Complexity: $O(N \log N)$ vì thuật toán gọi hàm sắp xếp.
- Space Complexity: $O(1)$ (hoặc ngầm $O(\log N)$ tùy vào bộ thu gom rác).

## 26. Shortest Unsorted Continuous Subarray (LeetCode 581)
**Đề bài chi tiết:** Cho một mảng `nums`, tìm một mảng con liên tiếp ngắn nhất mà nếu bạn sắp xếp riêng mảng con đó theo chiều tăng dần thì toàn bộ mảng `nums` cũng sẽ được sắp xếp.
**Phân tích thuật toán:**
Ý tưởng tham lam là tìm ranh giới tận cùng bị vi phạm trật tự tăng dần. Ta duyệt từ trái sang phải để giữ biến `max_seen`; nếu phần tử hiện tại bé hơn `max_seen`, nó đang nằm sai chỗ (phải xếp đằng sau `max_seen`), do đó nó nới rộng ranh giới bên phải của mảng con cần sort. Tương tự, duyệt từ phải qua trái với `min_seen`; nếu phần tử hiện tại lớn hơn `min_seen`, nó sai chỗ và kéo dài ranh giới bên trái.
**Mã nguồn Java:**
```java
public class ShortestUnsortedSubarray {
    public int findUnsortedSubarray(int[] nums) {
        int n = nums.length;
        int max = Integer.MIN_VALUE, min = Integer.MAX_VALUE;
        int left = -1, right = -2; // Khởi tạo độ dài là 0 nếu mảng đã sort sẵn
        
        for (int i = 0; i < n; i++) {
            max = Math.max(max, nums[i]);
            if (nums[i] < max) right = i;
        }
        
        for (int i = n - 1; i >= 0; i--) {
            min = Math.min(min, nums[i]);
            if (nums[i] > min) left = i;
        }
        
        return right - left + 1;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N)$ quét mảng 2 lần độc lập.
- Space Complexity: $O(1)$ không dùng mảng phụ.

## 27. Split Array into Consecutive Subsequences (LeetCode 659)
**Đề bài chi tiết:** Cho mảng `nums` được sắp xếp tăng dần. Bạn có thể chia mảng thành một hoặc nhiều chuỗi con (subsequences) sao cho mỗi chuỗi con bao gồm các số nguyên liên tiếp nhau và có độ dài tối thiểu là 3. Trả về `true` nếu có thể chia thỏa mãn, ngược lại `false`.
**Phân tích thuật toán:**
Dùng hai bảng băm (HashMap): `freq` lưu tần suất còn lại của mỗi số, và `appendfreq` lưu số lượng chuỗi con đang kết thúc tại một số và chờ đợi số liền kề tiếp theo. Khi gặp một số `x`, tham lam ưu tiên số một là nối `x` vào chuỗi đã tồn tại (nếu `appendfreq[x-1] > 0`). Nếu không nối được, ta mới phải bắt đầu khởi tạo một chuỗi mới gồm `x, x+1, x+2`. Nếu không đáp ứng được cả hai, trả về `false`.
**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;
public class SplitConsecutiveSubsequences {
    public boolean isPossible(int[] nums) {
        Map<Integer, Integer> freq = new HashMap<>();
        Map<Integer, Integer> appendfreq = new HashMap<>();
        for (int i : nums) freq.put(i, freq.getOrDefault(i, 0) + 1);
        
        for (int i : nums) {
            if (freq.get(i) == 0) continue;
            
            if (appendfreq.getOrDefault(i - 1, 0) > 0) {
                appendfreq.put(i - 1, appendfreq.get(i - 1) - 1);
                appendfreq.put(i, appendfreq.getOrDefault(i, 0) + 1);
            } else if (freq.getOrDefault(i + 1, 0) > 0 && freq.getOrDefault(i + 2, 0) > 0) {
                freq.put(i + 1, freq.get(i + 1) - 1);
                freq.put(i + 2, freq.get(i + 2) - 1);
                appendfreq.put(i + 2, appendfreq.getOrDefault(i + 2, 0) + 1);
            } else {
                return false;
            }
            freq.put(i, freq.get(i) - 1);
        }
        return true;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N)$ duyệt qua mảng vài vòng, thao tác trên HashMap là $O(1)$.
- Space Complexity: $O(N)$ lưu trữ trong bộ nhớ hai bảng HashMap.

## 28. Maximize Sum Of Array After K Negations (LeetCode 1005)
**Đề bài chi tiết:** Cho một mảng `nums` và số nguyên `k`. Bạn phải chọn đúng `k` lần một chỉ số `i` và đổi dấu phần tử đó `nums[i] = -nums[i]`. Bạn có thể lật một chỉ số nhiều lần. Hãy tìm tổng lớn nhất của mảng sau khi thực hiện các phép lật dấu.
**Phân tích thuật toán:**
Để cực đại hóa tổng, ta ưu tiên lật những số âm lớn nhất về giá trị tuyệt đối. Do đó, sắp xếp mảng theo thứ tự giá trị tuyệt đối giảm dần. Duyệt mảng, hễ gặp số âm và còn lượt `k`, ta lật ngược nó thành dương và giảm `k`. Khi kết thúc duyệt, nếu mảng toàn số dương mà `k` vẫn lẻ (lật chẵn lần thì hòa), ta lấy số dương nhỏ nhất ở cuối mảng để lật âm, do nó làm tổn thất tổng ít nhất.
**Mã nguồn Java:**
```java
import java.util.Arrays;
public class MaximizeSumKNegations {
    public int largestSumAfterKNegations(int[] nums, int k) {
        nums = Arrays.stream(nums)
                     .boxed()
                     .sorted((a, b) -> Math.abs(b) - Math.abs(a))
                     .mapToInt(i -> i)
                     .toArray();
                     
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] < 0 && k > 0) {
                nums[i] = -nums[i];
                k--;
            }
        }
        
        if (k % 2 == 1) {
            nums[nums.length - 1] = -nums[nums.length - 1];
        }
        
        int sum = 0;
        for (int num : nums) {
            sum += num;
        }
        return sum;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N \log N)$ với sắp xếp tùy chỉnh theo giá trị tuyệt đối.
- Space Complexity: $O(N)$ do dùng Stream API để box mảng. Có thể tối ưu thành $O(1)$ nếu sort mảng thủ công 2 lần.

## 29. Monotone Increasing Digits (LeetCode 738)
**Đề bài chi tiết:** Một số nguyên được gọi là "monotone increasing" nếu các chữ số của nó từ trái sang phải luôn không giảm (ví dụ `1234` hoặc `3334`). Cho số nguyên `n`, tìm số lớn nhất nhỏ hơn hoặc bằng `n` có tính chất này.
**Phân tích thuật toán:**
Chuyển đổi số thành chuỗi chữ số. Ta tìm từ phải sang trái, nếu gặp vị trí mà số đứng trước lớn hơn số đứng sau (`S[i-1] > S[i]`), ta biết rằng tính chất tăng dần đã bị phá vỡ. Để sửa chữa mà số thu được là lớn nhất, ta trừ chữ số `S[i-1]` đi 1 đơn vị, và đánh dấu mốc `marker = i`. Cuối cùng, mọi chữ số từ `marker` trở về bên phải sẽ bị ép thành `9` (tham lam giá trị tối đa cho nửa sau).
**Mã nguồn Java:**
```java
public class MonotoneIncreasingDigits {
    public int monotoneIncreasingDigits(int n) {
        char[] chars = String.valueOf(n).toCharArray();
        int marker = chars.length;
        for (int i = chars.length - 1; i > 0; i--) {
            if (chars[i] < chars[i - 1]) {
                marker = i;
                chars[i - 1]--;
            }
        }
        
        for (int i = marker; i < chars.length; i++) {
            chars[i] = '9';
        }
        return Integer.parseInt(new String(chars));
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(\log n)$ vì chiều dài chuỗi bằng số lượng chữ số của `n`.
- Space Complexity: $O(\log n)$ mảng ký tự lưu chữ số.

## 30. Queue Reconstruction by Height (LeetCode 406)
**Đề bài chi tiết:** Có một hàng đợi gồm những người được biểu diễn bằng mảng `people[i] = [h_i, k_i]`, trong đó `h_i` là chiều cao của người đó, và `k_i` là số người đứng phía trước có chiều cao lớn hơn hoặc bằng `h_i`. Hãy sắp xếp lại (reconstruct) mảng này sao cho các đặc tính `[h, k]` đều hợp lệ.
**Phân tích thuật toán:**
Chiến lược tham lam ưu tiên xếp những người cao nhất trước, vì sự xuất hiện của những người thấp hơn (chèn sau đó) sẽ không làm thay đổi giá trị `k` của những người cao hơn đã yên vị. Sắp xếp `people` theo chiều cao giảm dần, nếu bằng nhau thì `k` tăng dần. Sau đó chèn từng người vào danh sách kết quả tại chính vị trí index bằng `k` của họ.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
public class QueueReconstruction {
    public int[][] reconstructQueue(int[][] people) {
        Arrays.sort(people, (a, b) -> {
            if (a[0] == b[0]) return a[1] - b[1]; // k tăng dần
            return b[0] - a[0]; // Chiều cao giảm dần
        });
        
        List<int[]> result = new ArrayList<>();
        for (int[] p : people) {
            result.add(p[1], p);
        }
        
        return result.toArray(new int[people.length][]);
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N^2)$ vì hàm `add(index, element)` của ArrayList dịch chuyển các phần tử, tốn $O(N)$ trong vòng lặp $N$ lần.
- Space Complexity: $O(N)$ tạo danh sách kết quả trung gian trước khi chuyển sang Array.

