# 04. Bài tập Binary Search on Answers

Tài liệu này bao gồm 30 bài tập ứng dụng kỹ thuật Tìm kiếm nhị phân trên không gian kết quả. 10 bài đầu có phân tích chi tiết và code mẫu Java, 20 bài sau là phần tóm tắt mở rộng.

## 1. Koko Eating Bananas (LeetCode 875)
**Đề bài chi tiết:**
Cho mảng `piles` trong đó `piles[i]` là số quả chuối ở nải thứ `i`. Koko có thể ăn chuối với tốc độ `k` quả/giờ. Mỗi giờ cô ấy chọn 1 nải, ăn tối đa `k` quả. Nếu nải ít hơn `k` quả, cô ấy ăn hết và không ăn sang nải khác trong giờ đó. Tìm `k` nhỏ nhất để ăn hết tất cả chuối trong vòng `H` giờ.
**Phân tích thuật toán:**
- Search space cho `k`: Từ `1` (nhỏ nhất) đến `max(piles)` (lớn nhất, ăn 1 nải 1 giờ).
- Tính đơn điệu: Nếu tốc độ `k` thỏa mãn ăn xong trong $H$ giờ, thì tốc độ `k+1` chắc chắn thỏa mãn (`F F F T T T`). Bài toán Min-Max.
**Độ phức tạp:** $O(N \log M)$ với $N = \text{piles.length}$ và $M = \text{max(piles)}$.
**Mã nguồn Java:**
```java
class Solution {
    public int minEatingSpeed(int[] piles, int h) {
        int low = 1, high = 0;
        for (int p : piles) high = Math.max(high, p);
        int ans = -1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (isValid(piles, mid, h)) {
                ans = mid;
                high = mid - 1; // Tìm giá trị nhỏ hơn
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
    private boolean isValid(int[] piles, int speed, int h) {
        long time = 0;
        for (int p : piles) {
            time += (p + speed - 1) / speed; // Math.ceil(p / speed)
        }
        return time <= h;
    }
}
```

## 2. Capacity To Ship Packages Within D Days (LeetCode 1011)
**Đề bài chi tiết:**
Cần chuyển các gói hàng có trọng lượng mảng `weights` trong vòng `days` ngày. Xe tải phải chở hàng theo đúng thứ tự mảng. Mỗi ngày xe không thể chở quá trọng tải `capacity`. Tìm `capacity` tối thiểu.
**Phân tích thuật toán:**
- `low`: Khối lượng gói hàng lớn nhất `max(weights)`. Không thể nhỏ hơn vì sẽ không chở được gói đó.
- `high`: Tổng khối lượng toàn bộ hàng `sum(weights)` (chở hết trong 1 ngày).
- `isValid`: Duyệt mảng cộng dồn, nếu quá capacity thì cộng thêm 1 ngày.
**Độ phức tạp:** $O(N \log (\sum W - \max W))$.
**Mã nguồn Java:**
```java
class Solution {
    public int shipWithinDays(int[] weights, int days) {
        int low = 0, high = 0;
        for (int w : weights) {
            low = Math.max(low, w);
            high += w;
        }
        int ans = -1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (isValid(weights, mid, days)) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
    private boolean isValid(int[] weights, int capacity, int days) {
        int d = 1, load = 0;
        for (int w : weights) {
            if (load + w > capacity) {
                d++;
                load = w;
            } else {
                load += w;
            }
        }
        return d <= days;
    }
}
```

## 3. Split Array Largest Sum (LeetCode 410)
**Đề bài chi tiết:**
Chia mảng gồm số nguyên không âm thành `k` mảng con liên tiếp không rỗng, sao cho tổng lớn nhất của các mảng con là nhỏ nhất có thể.
**Phân tích thuật toán:**
Bài toán tương đương chính xác với bài 2 (Capacity To Ship Packages).
- `low` = `max(nums)`, `high` = `sum(nums)`.
**Độ phức tạp:** $O(N \log (\sum nums - \max nums))$.
**Mã nguồn Java:**
```java
class Solution {
    public int splitArray(int[] nums, int k) {
        int low = 0, high = 0;
        for (int num : nums) {
            low = Math.max(low, num);
            high += num;
        }
        int ans = low;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (canSplit(nums, mid, k)) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
    private boolean canSplit(int[] nums, int maxSum, int k) {
        int count = 1, currentSum = 0;
        for (int num : nums) {
            if (currentSum + num > maxSum) {
                count++;
                currentSum = num;
            } else {
                currentSum += num;
            }
        }
        return count <= k;
    }
}
```

## 4. Minimum Number of Days to Make m Bouquets (LeetCode 1482)
**Đề bài chi tiết:**
Cho mảng `bloomDay` trong đó `bloomDay[i]` là ngày hoa thứ `i` nở. Để làm 1 bó hoa cần `k` bông hoa **liên tiếp**. Tìm số ngày tối thiểu để làm được `m` bó hoa. Nếu không đủ hoa, trả về -1.
**Phân tích thuật toán:**
- Nếu `m * k > n`, không bao giờ làm đủ -> `return -1` (cần chú ý overflow khi nhân `m * k`).
- `low` = 1, `high` = `max(bloomDay)`.
- `isValid(day)`: Duyệt mảng, đếm các cụm liên tiếp các hoa nở `<= day` và có độ dài `k`.
**Độ phức tạp:** $O(N \log(\max(bloomDay)))$.
**Mã nguồn Java:**
```java
class Solution {
    public int minDays(int[] bloomDay, int m, int k) {
        int n = bloomDay.length;
        if ((long)m * k > n) return -1;
        int low = 1, high = 1;
        for (int day : bloomDay) high = Math.max(high, day);
        
        int ans = -1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (isValid(bloomDay, mid, m, k)) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
    private boolean isValid(int[] bloomDay, int day, int m, int k) {
        int bouquets = 0, consecutive = 0;
        for (int bloom : bloomDay) {
            if (bloom <= day) {
                consecutive++;
                if (consecutive == k) {
                    bouquets++;
                    consecutive = 0;
                }
            } else {
                consecutive = 0;
            }
        }
        return bouquets >= m;
    }
}
```

## 5. Aggressive Cows (Magnetic Force Between Two Balls - LeetCode 1552)
**Đề bài chi tiết:**
Có $N$ chuồng bò tại vị trí mảng `position`. Xếp `m` con bò vào các chuồng sao cho khoảng cách nhỏ nhất giữa 2 con bò bất kỳ là lớn nhất.
**Phân tích thuật toán:**
- Max-Min problem. Dạng: `T T T F F F`.
- Sắp xếp mảng `position`. `low` = 1, `high` = `position[n-1] - position[0]`.
- `isValid(dist)`: Xếp con bò đầu tiên ở `position[0]`. Duyệt để tìm các vị trí có khoảng cách `>= dist`. Nếu đếm đủ `m` con thì True.
**Độ phức tạp:** $O(N \log N + N \log D)$ với $D$ là khoảng cách max.
**Mã nguồn Java:**
```java
import java.util.Arrays;
class Solution {
    public int maxDistance(int[] position, int m) {
        Arrays.sort(position);
        int low = 1;
        int high = position[position.length - 1] - position[0];
        int ans = -1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (canPlace(position, m, mid)) {
                ans = mid;
                low = mid + 1; // Thử tìm khoảng cách lớn hơn
            } else {
                high = mid - 1;
            }
        }
        return ans;
    }
    private boolean canPlace(int[] position, int m, int dist) {
        int count = 1; // Đặt con đầu tiên
        int lastPos = position[0];
        for (int i = 1; i < position.length; i++) {
            if (position[i] - lastPos >= dist) {
                count++;
                lastPos = position[i];
            }
            if (count >= m) return true;
        }
        return false;
    }
}
```

## 6. Allocate Books (InterviewBit)
**Đề bài chi tiết:**
Cho mảng số trang của các quyển sách, chia sách cho $M$ học sinh sao cho số trang lớn nhất một học sinh phải đọc là nhỏ nhất. (Sách phải được giao liên tiếp).
**Phân tích thuật toán:**
- Hoàn toàn giống bài Split Array Largest Sum.
- Nếu số học sinh lớn hơn số quyển sách thì trả về -1.
**Độ phức tạp:** $O(N \log (\sum P - \max P))$.
**Mã nguồn Java:**
```java
public class Solution {
    public int books(int[] A, int B) {
        if (B > A.length) return -1;
        int low = 0, high = 0;
        for (int num : A) {
            low = Math.max(low, num);
            high += num;
        }
        int ans = -1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (isValid(A, B, mid)) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
    private boolean isValid(int[] A, int B, int maxPages) {
        int students = 1, currentPages = 0;
        for (int pages : A) {
            if (currentPages + pages > maxPages) {
                students++;
                currentPages = pages;
            } else {
                currentPages += pages;
            }
        }
        return students <= B;
    }
}
```

## 7. Median of Two Sorted Arrays (LeetCode 4)
**Đề bài chi tiết:**
Cho hai mảng đã sắp xếp `nums1` và `nums2` kích thước $m$ và $n$. Tìm trung vị của cả 2 mảng gộp lại.
**Phân tích thuật toán:**
- Dùng Binary Search tìm điểm "cắt" ở mảng ngắn hơn. Bài toán thuộc nhóm tìm boundary (phân loại đặc biệt của BS trên kết quả chia).
- `cut1 + cut2 = (m + n + 1) / 2`. Cần đảm bảo `maxLeft1 <= minRight2` và `maxLeft2 <= minRight1`.
**Độ phức tạp:** $O(\log(\min(m, n)))$.
**Mã nguồn Java:**
```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        if (nums1.length > nums2.length) return findMedianSortedArrays(nums2, nums1);
        int m = nums1.length, n = nums2.length;
        int low = 0, high = m;
        while (low <= high) {
            int cut1 = low + (high - low) / 2;
            int cut2 = (m + n + 1) / 2 - cut1;
            
            int l1 = (cut1 == 0) ? Integer.MIN_VALUE : nums1[cut1 - 1];
            int l2 = (cut2 == 0) ? Integer.MIN_VALUE : nums2[cut2 - 1];
            int r1 = (cut1 == m) ? Integer.MAX_VALUE : nums1[cut1];
            int r2 = (cut2 == n) ? Integer.MAX_VALUE : nums2[cut2];
            
            if (l1 <= r2 && l2 <= r1) {
                if ((m + n) % 2 == 0) {
                    return (Math.max(l1, l2) + Math.min(r1, r2)) / 2.0;
                } else {
                    return Math.max(l1, l2);
                }
            } else if (l1 > r2) {
                high = cut1 - 1;
            } else {
                low = cut1 + 1;
            }
        }
        return 0.0;
    }
}
```

## 8. Minimize Max Distance to Gas Station (LeetCode 774 - Premium)
**Đề bài chi tiết:**
Thêm `k` trạm xăng mới vào giữa các trạm hiện tại mảng `stations` sao cho khoảng cách lớn nhất giữa hai trạm kề nhau là nhỏ nhất. (Lưu ý: Khoảng cách là số thực).
**Phân tích thuật toán:**
- Floating-point binary search. `low = 0`, `high = max_diff`.
- `while (high - low > 1e-6)`.
- `isValid(dist)`: Số trạm cần thêm để mọi khoảng cách <= `dist` là $\sum \lfloor(stations[i] - stations[i-1]) / dist\rfloor$. Nếu tổng <= k là True.
**Độ phức tạp:** $O(N \log(\text{MaxDist} / 10^{-6}))$.
**Mã nguồn Java:**
```java
class Solution {
    public double minmaxGasDist(int[] stations, int k) {
        double low = 0, high = stations[stations.length - 1] - stations[0];
        while (high - low > 1e-6) {
            double mid = low + (high - low) / 2.0;
            if (isValid(stations, k, mid)) {
                high = mid; // Đối với số thực, gán bằng chính mid
            } else {
                low = mid;
            }
        }
        return low;
    }
    private boolean isValid(int[] stations, int k, double maxDist) {
        int count = 0;
        for (int i = 1; i < stations.length; i++) {
            count += (int)((stations[i] - stations[i - 1]) / maxDist);
        }
        return count <= k;
    }
}
```

## 9. Maximum Running Time of N Computers (LeetCode 2141)
**Đề bài chi tiết:**
Có $N$ máy tính và mảng pin `batteries`. Mỗi phút một máy tính dùng hết 1 đơn vị pin, và có thể đổi pin bất cứ lúc nào, một pin chỉ sạc cho một máy tại 1 thời điểm. Số phút lớn nhất tất cả $N$ máy cùng hoạt động là bao nhiêu?
**Phân tích thuật toán:**
- Tìm thời gian Max ($T$). `low` = 1, `high` = `sum(batteries) / N`.
- `isValid(time)`: Với mỗi pin, năng lượng cung cấp tối đa cho $T$ phút chỉ là $\min(battery, T)$. Nếu tổng năng lượng $\ge N \times T$ thì True. (Max-Min problem `T T T F F F`).
**Độ phức tạp:** $O(B \log(\sum B))$.
**Mã nguồn Java:**
```java
class Solution {
    public long maxRunTime(int n, int[] batteries) {
        long sum = 0;
        for (int b : batteries) sum += b;
        long low = 1, high = sum / n;
        long ans = -1;
        while (low <= high) {
            long mid = low + (high - low) / 2;
            if (isValid(batteries, n, mid)) {
                ans = mid;
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return ans;
    }
    private boolean isValid(int[] batteries, int n, long time) {
        long target = (long)n * time;
        long total = 0;
        for (int b : batteries) {
            total += Math.min((long)b, time);
        }
        return total >= target;
    }
}
```

## 10. Minimum Speed to Arrive on Time (LeetCode 1870)
**Đề bài chi tiết:**
Đi $N$ chuyến tàu có chiều dài mảng `dist`. Điểm đón mỗi chuyến chỉ tính theo **giờ nguyên**. Ví dụ đến sớm lúc 1.2 giờ thì phải đợi đến giờ thứ 2. Tìm tốc độ nhỏ nhất để đến nơi <= `hour` (giờ). Chuyến cuối cùng không phải đợi.
**Phân tích thuật toán:**
- Tìm Min `speed`. `low = 1, high = 10^7`.
- `isValid(speed)`: Thời gian = tổng `Math.ceil(dist[i]/speed)` với `n-1` trạm đầu, chuyến cuối tính là số thập phân `dist[n-1]/speed`.
**Độ phức tạp:** $O(N \log(10^7))$.
**Mã nguồn Java:**
```java
class Solution {
    public int minSpeedOnTime(int[] dist, double hour) {
        if (hour <= dist.length - 1) return -1;
        int low = 1, high = (int)1e7;
        int ans = -1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (isValid(dist, hour, mid)) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
    private boolean isValid(int[] dist, double hour, int speed) {
        double time = 0;
        for (int i = 0; i < dist.length - 1; i++) {
            time += Math.ceil((double)dist[i] / speed);
        }
        time += (double)dist[dist.length - 1] / speed;
        return time <= hour;
    }
}
```

## 11. H-Index II (LeetCode 275)
**Đề bài chi tiết:**
Cho một mảng `citations` chứa số trích dẫn của các bài báo của một nhà nghiên cứu, đã được sắp xếp theo thứ tự tăng dần. Tìm h-index của nhà nghiên cứu đó. H-index là giá trị lớn nhất $h$ sao cho nhà nghiên cứu có ít nhất $h$ bài báo, mỗi bài có từ $h$ trích dẫn trở lên.
**Phân tích thuật toán:**
- Gọi $N$ là độ dài mảng. Giá trị $h$ tương ứng tại vị trí `i` là `N - i` (số bài báo có trích dẫn lớn hơn hoặc bằng `citations[i]`).
- Cần tìm giá trị nhỏ nhất của `i` sao cho `citations[i] >= N - i`. Khi đó h-index có thể đạt mức `N - i`.
- Sử dụng Binary Search: `low = 0`, `high = N - 1`. Nếu `citations[mid] >= N - mid`, ta ghi nhận đáp án và thử tìm mốc thấp hơn `high = mid - 1` để mở rộng số lượng bài báo.
**Độ phức tạp:** Thời gian: $O(\log N)$, Không gian: $O(1)$.
**Mã nguồn Java:**
```java
class Solution {
    public int hIndex(int[] citations) {
        int n = citations.length;
        int low = 0, high = n - 1;
        int ans = 0;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (citations[mid] >= n - mid) {
                ans = n - mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
}
```

## 12. Find the Smallest Divisor Given a Threshold (LeetCode 1283)
**Đề bài chi tiết:**
Cho mảng số nguyên `nums` và số nguyên `threshold`. Chúng ta sẽ chọn một số nguyên dương `divisor` (số chia), đem chia tất cả phần tử trong mảng cho số này và tính tổng (kết quả mỗi phép chia làm tròn lên đến số nguyên gần nhất). Tìm `divisor` nhỏ nhất sao cho tổng nhỏ hơn hoặc bằng `threshold`.
**Phân tích thuật toán:**
- Không gian tìm kiếm: `low = 1`, `high = max(nums)`.
- Hàm kiểm tra `isValid(divisor)`: tính tổng $\sum \lceil nums[i] / divisor \rceil$. Nếu tổng $\le threshold$, ta ghi nhận và thử tìm số chia nhỏ hơn (giảm `high`). Ngược lại, tăng `low`.
- Tính đơn điệu: số chia càng lớn thì tổng càng nhỏ. `F F F T T T`, cần tìm `T` đầu tiên.
**Độ phức tạp:** $O(N \log M)$ với $M = \max(nums)$.
**Mã nguồn Java:**
```java
class Solution {
    public int smallestDivisor(int[] nums, int threshold) {
        int low = 1, high = 0;
        for (int num : nums) high = Math.max(high, num);
        
        int ans = -1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (isValid(nums, mid, threshold)) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
    
    private boolean isValid(int[] nums, int divisor, int threshold) {
        int sum = 0;
        for (int num : nums) {
            sum += (num + divisor - 1) / divisor; // Math.ceil(num / divisor)
        }
        return sum <= threshold;
    }
}
```

## 13. Kth Missing Positive Number (LeetCode 1539)
**Đề bài chi tiết:**
Cho mảng số nguyên dương `arr` đã sắp xếp tăng dần và số nguyên `k`. Tìm số nguyên dương bị thiếu thứ `k` nếu ta duyệt các số từ $1, 2, 3, \dots$.
**Phân tích thuật toán:**
- Tại chỉ số `i`, nếu không có số bị thiếu nào, `arr[i]` phải bằng `i + 1`. Số lượng phần tử bị thiếu tính đến vị trí `i` là `arr[i] - (i + 1)`.
- Ta tìm kiếm nhị phân trên chỉ số mảng: `low = 0`, `high = n - 1`. 
- Nếu `arr[mid] - (mid + 1) < k`, nghĩa là số bị thiếu thứ `k` nằm ở bên phải `mid`, nên `low = mid + 1`.
- Nếu $\ge k$, nó nằm ở bên trái, `high = mid - 1`.
- Kết thúc Binary Search, đáp án là `low + k`.
**Độ phức tạp:** $O(\log N)$.
**Mã nguồn Java:**
```java
class Solution {
    public int findKthPositive(int[] arr, int k) {
        int low = 0, high = arr.length - 1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            int missing = arr[mid] - (mid + 1);
            
            if (missing < k) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return low + k;
    }
}
```

## 14. Maximum Candies Allocated to K Children (LeetCode 2226)
**Đề bài chi tiết:**
Có một số đống kẹo được lưu trong mảng `candies`. Bạn muốn chia kẹo cho `k` đứa trẻ sao cho mỗi đứa trẻ nhận được cùng một số kẹo và tất cả số kẹo một đứa trẻ nhận được phải đến từ cùng một đống (có thể chia đống, nhưng không được gộp các đống). Tìm số kẹo tối đa một đứa trẻ có thể nhận.
**Phân tích thuật toán:**
- Bài toán tìm Max kẹo, không gian: `low = 1`, `high = max(candies)`.
- Cần chú ý trường hợp không thể chia kẹo cho trẻ (trả về 0) -> dùng biến `low` bắt đầu từ 1, hoặc Binary search trong không gian `1` đến `10^7`.
- Hàm `isValid(amount)`: đếm số lượng trẻ em có thể nhận `amount` kẹo bằng công thức $\sum \lfloor candies[i] / amount \rfloor$. Nếu tổng này $\ge k$, ta tăng lượng kẹo (`low = mid + 1`).
**Độ phức tạp:** $O(N \log M)$ với $M = \max(candies)$.
**Mã nguồn Java:**
```java
class Solution {
    public int maximumCandies(int[] candies, long k) {
        int low = 1, high = 0;
        for (int c : candies) high = Math.max(high, c);
        
        int ans = 0;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (isValid(candies, mid, k)) {
                ans = mid;
                low = mid + 1; // Thử lấy nhiều kẹo hơn
            } else {
                high = mid - 1;
            }
        }
        return ans;
    }
    
    private boolean isValid(int[] candies, int amount, long k) {
        long children = 0;
        for (int c : candies) {
            children += c / amount;
        }
        return children >= k;
    }
}
```

## 15. Minimum Time to Complete Trips (LeetCode 2187)
**Đề bài chi tiết:**
Có mảng `time` lưu thời gian một chiếc xe buýt hoàn thành một chuyến đi. Bạn cần tìm thời gian tối thiểu để tất cả xe buýt cùng hoàn thành tổng cộng ít nhất `totalTrips` chuyến đi. Mỗi xe buýt hoạt động độc lập.
**Phân tích thuật toán:**
- Không gian tìm kiếm: Thời gian từ `1` đến giá trị rất lớn. `low = 1`, `high = (long) min(time) * totalTrips` (trường hợp dùng toàn bộ xe chạy nhanh nhất).
- Hàm `isValid(t)`: Tính tổng chuyến xe đi được trong thời gian `t`: $\sum \lfloor t / time[i] \rfloor$. Nếu tổng $\ge totalTrips$, thu hẹp `high = mid - 1`.
**Độ phức tạp:** $O(N \log M)$ với $M$ là thời gian tối đa.
**Mã nguồn Java:**
```java
class Solution {
    public long minimumTime(int[] time, int totalTrips) {
        long minTime = Integer.MAX_VALUE;
        for (int t : time) {
            minTime = Math.min(minTime, t);
        }
        
        long low = 1, high = minTime * totalTrips;
        long ans = high;
        
        while (low <= high) {
            long mid = low + (high - low) / 2;
            if (isValid(time, mid, totalTrips)) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
    
    private boolean isValid(int[] time, long givenTime, int totalTrips) {
        long trips = 0;
        for (int t : time) {
            trips += givenTime / t;
            if (trips >= totalTrips) return true; // Tránh tràn số
        }
        return trips >= totalTrips;
    }
}
```

## 16. Sqrt(x) (LeetCode 69)
**Đề bài chi tiết:**
Tính và trả về căn bậc hai của số nguyên không âm `x`. Vì kết quả trả về là số nguyên, các phần thập phân sẽ bị loại bỏ. Chú ý: Không sử dụng hàm built-in `sqrt`.
**Phân tích thuật toán:**
- Tìm $k$ lớn nhất sao cho $k^2 \le x$.
- Không gian: `low = 1`, `high = x`.
- Ở mỗi bước, tính `mid = low + (high - low) / 2`. Nếu $mid \times mid \le x$, ta giữ đáp án và tăng `low = mid + 1` để tìm $k$ lớn hơn. Nếu $mid \times mid > x$, giảm `high = mid - 1`. Cần chú ý overflow khi tính $mid \times mid$.
**Độ phức tạp:** $O(\log x)$.
**Mã nguồn Java:**
```java
class Solution {
    public int mySqrt(int x) {
        if (x == 0) return 0;
        int low = 1, high = x;
        int ans = 1;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if ((long) mid * mid <= x) {
                ans = mid;
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return ans;
    }
}
```

## 17. Valid Perfect Square (LeetCode 367)
**Đề bài chi tiết:**
Cho số nguyên dương `num`, viết hàm trả về `true` nếu `num` là số chính phương, nếu không trả về `false`. Không dùng hàm toán học có sẵn.
**Phân tích thuật toán:**
- Tương tự bài Sqrt(x), ta dùng Binary Search để kiểm tra xem có tồn tại số nguyên $mid$ nào mà $mid^2 = num$ không.
- Khoảng tìm kiếm: `low = 1`, `high = num`. Nếu $mid \times mid == num$ trả về `true`.
- Cần ép kiểu `long` để tránh tràn số học.
**Độ phức tạp:** $O(\log N)$.
**Mã nguồn Java:**
```java
class Solution {
    public boolean isPerfectSquare(int num) {
        if (num < 1) return false;
        long low = 1, high = num;
        
        while (low <= high) {
            long mid = low + (high - low) / 2;
            long square = mid * mid;
            
            if (square == num) {
                return true;
            } else if (square < num) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return false;
    }
}
```

## 18. Search a 2D Matrix (LeetCode 74)
**Đề bài chi tiết:**
Viết thuật toán tìm kiếm hiệu quả một giá trị `target` trong ma trận `m x n` được sắp xếp sao cho:
1. Các số trong mỗi hàng được sắp xếp từ trái sang phải.
2. Số nguyên đầu tiên của mỗi hàng lớn hơn số nguyên cuối cùng của hàng trước đó.
**Phân tích thuật toán:**
- Tính chất ma trận gợi ý nó hoàn toàn tương đương với một mảng 1 chiều đã được sắp xếp nếu nối các hàng lại.
- Giả sử ma trận `m x n`, phần tử ở chỉ số `index` (từ $0$ đến $m \times n - 1$) trong mảng 1 chiều sẽ tương ứng với tọa độ `[index / n][index % n]` trong ma trận.
- Áp dụng Binary Search trực tiếp trên đoạn `[0, m * n - 1]`.
**Độ phức tạp:** $O(\log (m \times n))$.
**Mã nguồn Java:**
```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return false;
        int m = matrix.length;
        int n = matrix[0].length;
        
        int low = 0, high = m * n - 1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            int midValue = matrix[mid / n][mid % n];
            
            if (midValue == target) {
                return true;
            } else if (midValue < target) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return false;
    }
}
```

## 19. Find Peak Element (LeetCode 162)
**Đề bài chi tiết:**
Phần tử đỉnh (peak element) là phần tử lớn hơn nghiêm ngặt so với các phần tử kề nó. Cho một mảng `nums`, tìm đỉnh và trả về chỉ số của nó. Mảng có thể chứa nhiều đỉnh, trả về một đỉnh bất kỳ. `nums[-1] = nums[n] = -∞`.
**Phân tích thuật toán:**
- Mặc dù mảng không được sắp xếp hoàn toàn, ta vẫn có thể dùng Binary Search vì tính chất địa phương: nếu `nums[mid] < nums[mid + 1]`, điều này có nghĩa là mảng đang đi lên và chắc chắn sẽ có đỉnh ở phía bên phải `mid` (có thể là phần tử kề hoặc phần tử ở tận cùng mảng).
- Nếu ngược lại `nums[mid] > nums[mid + 1]`, thì đỉnh nằm ở phía bên trái (bao gồm cả `mid`).
**Độ phức tạp:** $O(\log N)$.
**Mã nguồn Java:**
```java
class Solution {
    public int findPeakElement(int[] nums) {
        int low = 0, high = nums.length - 1;
        
        while (low < high) {
            int mid = low + (high - low) / 2;
            // So sánh với phần tử bên phải
            if (nums[mid] < nums[mid + 1]) {
                low = mid + 1; // Đi theo dốc lên
            } else {
                high = mid; // Đỉnh ở bên trái hoặc chính là mid
            }
        }
        return low;
    }
}
```

## 20. Peak Index in a Mountain Array (LeetCode 852)
**Đề bài chi tiết:**
Một mảng `arr` được gọi là núi nếu nó tăng dần cho đến đỉnh rồi giảm dần. Trả về chỉ số của phần tử đỉnh trong mảng.
**Phân tích thuật toán:**
- Đây là một trường hợp đặc biệt của bài Find Peak Element (LeetCode 162). Chỉ có duy nhất 1 đỉnh.
- Áp dụng logic tương tự, dùng Binary Search. Nếu `arr[mid] < arr[mid + 1]`, đoạn này đang tăng, đỉnh ở bên phải. Ngược lại đoạn này đang giảm, đỉnh ở bên trái.
**Độ phức tạp:** $O(\log N)$.
**Mã nguồn Java:**
```java
class Solution {
    public int peakIndexInMountainArray(int[] arr) {
        int low = 0, high = arr.length - 1;
        
        while (low < high) {
            int mid = low + (high - low) / 2;
            if (arr[mid] < arr[mid + 1]) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }
        return low;
    }
}
```

## 21. Single Element in a Sorted Array (LeetCode 540)
**Đề bài chi tiết:**
Bạn được cung cấp một mảng các số nguyên đã được sắp xếp `nums`, trong đó mọi phần tử xuất hiện đúng hai lần, ngoại trừ một phần tử xuất hiện đúng một lần. Tìm phần tử duy nhất xuất hiện một lần đó. Thuật toán của bạn phải chạy với độ phức tạp $O(\log N)$ và $O(1)$ bộ nhớ.
**Phân tích thuật toán:**
- Mảng có độ dài lẻ. Các phần tử thành từng cặp. 
- Trước phần tử đơn độc, cặp đầu tiên ở chỉ số chẵn, phần tử thứ hai ở chỉ số lẻ: `nums[even] == nums[even+1]`.
- Sau phần tử đơn độc, trật tự bị đảo lộn: cặp đầu tiên ở chỉ số lẻ, phần tử thứ hai ở chỉ số chẵn.
- Dùng Binary Search: `low = 0`, `high = n - 2`. Nếu `mid` là lẻ, ta trừ 1 để đưa về chỉ số chẵn. Sau đó so sánh `nums[mid]` với `nums[mid+1]`. Nếu bằng nhau, phần tử đơn độc nằm ở bên phải (`low = mid + 2`). Nếu khác nhau, phần tử đơn độc nằm bên trái hoặc chính là `mid` (`high = mid`).
**Độ phức tạp:** $O(\log N)$ thời gian.
**Mã nguồn Java:**
```java
class Solution {
    public int singleNonDuplicate(int[] nums) {
        int low = 0, high = nums.length - 2;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (mid % 2 == 1) mid--; // Đưa về index chẵn
            
            if (nums[mid] == nums[mid + 1]) {
                low = mid + 2; // Nửa trái hợp lệ, lỗi ở nửa phải
            } else {
                high = mid - 1; // Lỗi ở nửa trái hoặc chính tại mid
            }
        }
        return nums[low];
    }
}
```

## 22. Path With Minimum Effort (LeetCode 1631)
**Đề bài chi tiết:**
Bạn là một người đi bộ đường dài chuẩn bị cho chuyến đi. Mảng 2D `heights` có kích thước `rows x columns`. Bạn xuất phát từ `(0, 0)` và muốn đến `(rows-1, columns-1)`. Độ khó (effort) của một đường đi là chênh lệch chiều cao lớn nhất giữa hai ô kề nhau trên đường đi đó. Tìm nỗ lực nhỏ nhất để đi từ góc trái trên xuống góc phải dưới.
**Phân tích thuật toán:**
- Không gian kết quả là sự chênh lệch chiều cao, có giá trị từ `0` đến `10^6` (hoặc chênh lệch lớn nhất có thể).
- `low = 0`, `high = 10^6`.
- `isValid(effort)`: Dùng BFS hoặc DFS từ ô `(0,0)` đến `(rows-1, columns-1)`. Tại mỗi bước, ta chỉ có thể di chuyển sang ô kề cạnh nếu chênh lệch chiều cao tuyệt đối $\le effort$. Nếu tìm được đường đến đích, trả về True.
**Độ phức tạp:** $O(R \times C \log(\text{MaxEffort}))$.
**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    int[] dir = {0, 1, 0, -1, 0};
    
    public int minimumEffortPath(int[][] heights) {
        int low = 0, high = 1000000;
        int ans = high;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (isValid(heights, mid)) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
    
    private boolean isValid(int[][] heights, int effort) {
        int m = heights.length, n = heights[0].length;
        boolean[][] visited = new boolean[m][n];
        Queue<int[]> q = new LinkedList<>();
        q.offer(new int[]{0, 0});
        visited[0][0] = true;
        
        while (!q.isEmpty()) {
            int[] curr = q.poll();
            int r = curr[0], c = curr[1];
            if (r == m - 1 && c == n - 1) return true;
            
            for (int i = 0; i < 4; i++) {
                int nr = r + dir[i], nc = c + dir[i + 1];
                if (nr >= 0 && nr < m && nc >= 0 && nc < n && !visited[nr][nc]) {
                    if (Math.abs(heights[nr][nc] - heights[r][c]) <= effort) {
                        visited[nr][nc] = true;
                        q.offer(new int[]{nr, nc});
                    }
                }
            }
        }
        return false;
    }
}
```

## 23. Swim in Rising Water (LeetCode 778)
**Đề bài chi tiết:**
Cho ma trận `grid` `N x N`. Mỗi ô `grid[i][j]` chứa một giá trị duy nhất là độ cao của ô đó. Nước dâng lên theo thời gian `t`. Tại thời điểm `t`, mực nước ở khắp nơi là `t`. Bạn chỉ có thể bơi từ ô này sang ô kề cạnh nếu tại thời điểm `t`, độ cao của cả hai ô đều $\le t$. Tìm thời điểm `t` nhỏ nhất để bơi từ `(0, 0)` đến `(N-1, N-1)`.
**Phân tích thuật toán:**
- Giống như bài Path With Minimum Effort.
- Không gian kết quả cho thời gian `t` là từ `grid[0][0]` hoặc `0` đến `N^2 - 1`.
- Dùng Binary Search tìm `t`. Với một giá trị `mid`, BFS xem có đường đi từ `(0,0)` đến `(N-1, N-1)` sao cho mọi ô đi qua có `grid[r][c] <= mid`.
**Độ phức tạp:** $O(N^2 \log (\max(grid)))$.
**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    int[] dir = {0, 1, 0, -1, 0};
    
    public int swimInWater(int[][] grid) {
        int n = grid.length;
        int low = grid[0][0], high = n * n - 1;
        int ans = high;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (isValid(grid, mid)) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
    
    private boolean isValid(int[][] grid, int time) {
        int n = grid.length;
        if (grid[0][0] > time) return false;
        
        boolean[][] visited = new boolean[n][n];
        Queue<int[]> q = new LinkedList<>();
        q.offer(new int[]{0, 0});
        visited[0][0] = true;
        
        while (!q.isEmpty()) {
            int[] curr = q.poll();
            int r = curr[0], c = curr[1];
            if (r == n - 1 && c == n - 1) return true;
            
            for (int i = 0; i < 4; i++) {
                int nr = r + dir[i], nc = c + dir[i + 1];
                if (nr >= 0 && nr < n && nc >= 0 && nc < n && !visited[nr][nc]) {
                    if (grid[nr][nc] <= time) {
                        visited[nr][nc] = true;
                        q.offer(new int[]{nr, nc});
                    }
                }
            }
        }
        return false;
    }
}
```

## 24. Kth Smallest Element in a Sorted Matrix (LeetCode 378)
**Đề bài chi tiết:**
Cho ma trận vuông `matrix` `n x n` nơi mỗi hàng và mỗi cột được sắp xếp theo thứ tự tăng dần, tìm phần tử nhỏ thứ `k` trong ma trận. Cần tìm một giải pháp có độ phức tạp tốt hơn $O(N^2 \log(N^2))$.
**Phân tích thuật toán:**
- Không gian tìm kiếm là giá trị của các phần tử: `low = matrix[0][0]`, `high = matrix[n-1][n-1]`.
- Tại mỗi giá trị `mid`, đếm số lượng phần tử nhỏ hơn hoặc bằng `mid` trong ma trận. Nếu số lượng này $\ge k$, điều đó nghĩa là đáp án có thể là `mid` hoặc một số nhỏ hơn. Ngược lại, đáp án phải lớn hơn `mid`.
- Do hàng và cột đã được sắp xếp, việc đếm có thể thực hiện hiệu quả bằng cách bắt đầu từ góc trái dưới `(n-1, 0)`.
**Độ phức tạp:** $O(N \log(\max - \min))$.
**Mã nguồn Java:**
```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int low = matrix[0][0], high = matrix[n - 1][n - 1];
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            int count = countLessOrEqual(matrix, mid);
            
            if (count >= k) {
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return low;
    }
    
    private int countLessOrEqual(int[][] matrix, int target) {
        int n = matrix.length;
        int count = 0;
        int r = n - 1, c = 0; // Bắt đầu từ góc dưới cùng bên trái
        
        while (r >= 0 && c < n) {
            if (matrix[r][c] <= target) {
                count += r + 1; // Cả cột từ 0 đến r đều <= target
                c++;
            } else {
                r--;
            }
        }
        return count;
    }
}
```

## 25. K-th Smallest Prime Fraction (LeetCode 786)
**Đề bài chi tiết:**
Cho mảng `arr` được sắp xếp gồm 1 và các số nguyên tố. Phân số dạng `arr[i] / arr[j]` với $0 \le i < j < arr.length$. Tìm phân số nhỏ thứ `k` trong số tất cả các phân số có thể tạo ra.
**Phân tích thuật toán:**
- Tìm kiếm nhị phân trên tập số thực (khoảng `[0.0, 1.0]`).
- Đếm số lượng phân số nhỏ hơn hoặc bằng `mid`. Dùng hai con trỏ để đếm trong độ phức tạp $O(N)$.
- Tại mỗi bước, nếu đếm được đúng `k` phân số, ghi nhận phân số lớn nhất thỏa mãn $\le mid$. Để làm việc này ta duy trì giá trị `maxFraction` ở mỗi lần gọi hàm.
- Có thể lặp số lần cố định hoặc lặp dựa trên độ chênh lệch.
**Độ phức tạp:** $O(N \log (\text{Precision}))$.
**Mã nguồn Java:**
```java
class Solution {
    public int[] kthSmallestPrimeFraction(int[] arr, int k) {
        double low = 0.0, high = 1.0;
        int n = arr.length;
        
        while (low < high) {
            double mid = low + (high - low) / 2;
            int count = 0;
            int p = 0, q = 1;
            int j = 1;
            
            for (int i = 0; i < n - 1; i++) {
                while (j < n && arr[i] > mid * arr[j]) {
                    j++;
                }
                count += n - j;
                if (j < n && p * arr[j] < q * arr[i]) { // (p/q < arr[i]/arr[j])
                    p = arr[i];
                    q = arr[j];
                }
            }
            
            if (count == k) return new int[]{p, q};
            else if (count < k) low = mid;
            else high = mid;
        }
        return new int[]{};
    }
}
```

## 26. Kth Pair Distance (LeetCode 719)
**Đề bài chi tiết:**
Cho một mảng `nums` và số nguyên `k`. Khoảng cách giữa hai phần tử là giá trị tuyệt đối của hiệu số giữa chúng. Tìm khoảng cách nhỏ thứ `k` giữa các cặp phần tử.
**Phân tích thuật toán:**
- Sắp xếp mảng `nums`.
- Khoảng cách bé nhất là `0`, lớn nhất là `nums[n-1] - nums[0]`.
- Binary Search tìm một khoảng cách `mid`. Ta đếm xem có bao nhiêu cặp có khoảng cách $\le mid$.
- Đếm số cặp có hiệu $\le mid$ bằng thuật toán Sliding Window với 2 con trỏ `i` và `j`. Do mảng đã sắp xếp, với mỗi `j`, ta tăng `i` cho đến khi `nums[j] - nums[i] <= mid`, số lượng cặp là `j - i`.
**Độ phức tạp:** $O(N \log N + N \log(\max - \min))$.
**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums);
        int n = nums.length;
        int low = 0, high = nums[n - 1] - nums[0];
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (countPairs(nums, mid) >= k) {
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return low;
    }
    
    private int countPairs(int[] nums, int diff) {
        int count = 0;
        int i = 0;
        for (int j = 1; j < nums.length; j++) {
            while (nums[j] - nums[i] > diff) {
                i++;
            }
            count += j - i;
        }
        return count;
    }
}
```

## 27. Divide Chocolate (LeetCode 1231 - Premium)
**Đề bài chi tiết:**
Bạn có một thanh sô cô la gồm các mảng, mỗi mảng có một độ ngọt riêng trong mảng `sweetness`. Bạn muốn chia sô cô la cho bạn và `k` người bạn (tổng cộng `k+1` phần), bằng cách cắt thanh sô cô la tại các điểm liên tiếp. Bạn chia sao cho độ ngọt của phần ít ngọt nhất của bạn là lớn nhất.
**Phân tích thuật toán:**
- Tương tự như bài Split Array Largest Sum nhưng tìm Max-Min (chia mảng sao cho tổng phần nhỏ nhất là lớn nhất).
- `low = 1`, `high = sum(sweetness) / (k + 1)`.
- `isValid(mid)`: Đếm số phần có thể cắt mà mỗi phần có tổng $\ge mid$. Nếu số phần $\ge k + 1$, ta có thể tăng `mid` (`low = mid + 1`).
**Độ phức tạp:** $O(N \log (\sum sweetness))$.
**Mã nguồn Java:**
```java
class Solution {
    public int maximizeSweetness(int[] sweetness, int k) {
        int low = 1, high = 0;
        for (int s : sweetness) high += s;
        high /= (k + 1);
        
        int ans = 0;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (isValid(sweetness, mid, k + 1)) {
                ans = mid;
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return ans;
    }
    
    private boolean isValid(int[] sweetness, int target, int pieces) {
        int currentSum = 0;
        int count = 0;
        for (int s : sweetness) {
            currentSum += s;
            if (currentSum >= target) {
                count++;
                currentSum = 0;
            }
        }
        return count >= pieces;
    }
}
```

## 28. Magnetic Force Between Two Balls (LeetCode 1552)
**Đề bài chi tiết:**
Có $N$ vị trí lưu trong mảng `position`. Ta muốn đặt `m` quả bóng vào các vị trí sao cho lực đẩy từ tính nhỏ nhất giữa hai quả bóng bất kỳ là lớn nhất. Lực được định nghĩa bằng khoảng cách $|position[i] - position[j]|$.
**Phân tích thuật toán:**
- Bài toán này giống hệt bài **Aggressive Cows** (Bài 5).
- Sắp xếp mảng `position`.
- Binary Search tìm khoảng cách Max-Min: `low = 1`, `high = position[N-1] - position[0]`.
- Đếm số lượng bóng có thể đặt cách nhau ít nhất `mid`. Nếu đặt đủ `m` bóng, `low = mid + 1`.
**Độ phức tạp:** $O(N \log N + N \log(\max(\text{position})))$.
**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int maxDistance(int[] position, int m) {
        Arrays.sort(position);
        int low = 1;
        int high = position[position.length - 1] - position[0];
        
        int ans = -1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (canPlace(position, m, mid)) {
                ans = mid;
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return ans;
    }
    
    private boolean canPlace(int[] position, int m, int dist) {
        int count = 1;
        int lastPos = position[0];
        for (int i = 1; i < position.length; i++) {
            if (position[i] - lastPos >= dist) {
                count++;
                lastPos = position[i];
            }
            if (count == m) return true;
        }
        return false;
    }
}
```

## 29. Find K-th Smallest Pair Distance (LeetCode 719)
*(Lưu ý: Bài tập này được nhắc lại trong danh sách tóm tắt, là cùng một bài toán với câu 26. Dưới đây trình bày cách viết gọn hơn cho hàm kiểm tra Sliding Window).*
**Đề bài chi tiết:**
Như câu 26: Tìm khoảng cách nhỏ thứ `k` giữa các cặp trong mảng.
**Phân tích thuật toán:**
- Giải thuật hoàn toàn trùng khớp với bài 26. Thay vì dùng một hàm đếm riêng biệt, ta có thể tích hợp vòng lặp vào trong vòng lặp Binary Search.
- Ta nhắc lại tầm quan trọng của việc sắp xếp. Không có sắp xếp, ta không thể đếm $O(N)$ bằng hai con trỏ.
**Độ phức tạp:** $O(N \log N + N \log(\max - \min))$.
**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums);
        int n = nums.length;
        int low = 0, high = nums[n - 1] - nums[0];
        
        while (low < high) { // Chú ý: dùng low < high, không cần biến ans
            int mid = low + (high - low) / 2;
            int count = 0, i = 0;
            
            for (int j = 1; j < n; j++) {
                while (nums[j] - nums[i] > mid) i++;
                count += j - i;
            }
            
            if (count >= k) {
                high = mid; // Đáp án có thể chính là mid
            } else {
                low = mid + 1;
            }
        }
        return low;
    }
}
```

## 30. Maximum Number of Groups Entering a Competition (LeetCode 2358)
**Đề bài chi tiết:**
Bạn có một mảng `grades` gồm điểm của các học sinh. Bạn cần phân chia họ thành nhiều nhóm thỏa mãn 2 điều kiện:
1. Nhóm $i$ phải có ít người hơn nhóm $i+1$.
2. Tổng điểm của nhóm $i$ phải nhỏ hơn nhóm $i+1$.
Tìm số nhóm lớn nhất có thể tạo ra.
**Phân tích thuật toán:**
- Nếu ta chỉ quan tâm đến kích thước nhóm: $1, 2, 3, 4 \dots$, thì nếu phân đúng như vậy, ta luôn có thể làm tổng điểm nhóm sau lớn hơn nhóm trước (bằng cách sắp xếp `grades` và phân cho nhóm ít người những điểm nhỏ, nhóm nhiều người điểm lớn).
- Vậy bài toán thực chất chỉ là tìm số $k$ lớn nhất sao cho tổng $1 + 2 + \dots + k \le N$ (với $N$ là số lượng học sinh).
- Có thể dùng toán học (phương trình bậc 2) hoặc Binary Search.
- Với BS: Không gian tìm kiếm `low = 1`, `high = 1000` (vì $1000 \times 1001 / 2 > 10^5$).
**Độ phức tạp:** $O(\log \sqrt{N})$ bằng BS hoặc $O(1)$ nếu dùng công thức Toán học.
**Mã nguồn Java:**
```java
class Solution {
    public int maximumGroups(int[] grades) {
        int n = grades.length;
        int low = 1, high = n;
        int ans = 1;
        
        while (low <= high) {
            long mid = low + (high - low) / 2;
            long required = mid * (mid + 1) / 2;
            
            if (required <= n) {
                ans = (int) mid;
                low = mid + 1; // Thử tăng số nhóm
            } else {
                high = mid - 1;
            }
        }
        return ans;
    }
}
```
