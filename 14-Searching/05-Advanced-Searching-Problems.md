# Các bài tập Advanced Searching (Tìm kiếm Nâng cao)

## 1. Peak Index in a Mountain Array
**Đề bài chi tiết:**
Cho một mảng nguyên `arr` đảm bảo là một "mảng núi" (mountain array). Mảng `arr` được gọi là mảng núi nếu có độ dài ít nhất 3 và tồn tại một chỉ số `i` (0 < i < arr.length - 1) sao cho:
- `arr[0] < arr[1] < ... < arr[i - 1] < arr[i]`
- `arr[i] > arr[i + 1] > ... > arr[arr.length - 1]`
Trả về chỉ số `i` của đỉnh núi. Yêu cầu độ phức tạp thời gian là $O(\log n)$.

**Phân tích thuật toán:**
Do đặc tính tăng rồi giảm, mảng có tính chất đơn điệu theo đoạn. Ta có thể dùng Binary Search: nếu `arr[mid] < arr[mid + 1]`, ta đang ở sườn tăng (bên trái đỉnh), vậy đỉnh nằm bên phải `mid`. Ngược lại, đỉnh nằm từ `mid` trở về trái.

**Mã nguồn Java:**
```java
public class Solution {
    public int peakIndexInMountainArray(int[] arr) {
        int left = 0;
        int right = arr.length - 1;
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (arr[mid] < arr[mid + 1]) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        
        return left;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(\log n)$
- Không gian (Space Complexity): $O(1)$

---

## 2. Find Peak Element
**Đề bài chi tiết:**
Cho một mảng số nguyên `nums` có thể chứa nhiều đỉnh, hãy tìm chỉ số của bất kỳ đỉnh nào. Một phần tử đỉnh là phần tử lớn hơn các phần tử kề cạnh nó (xung quanh nó). Giả sử `nums[-1] = nums[n] = -∞`. Đảm bảo hai phần tử liên tiếp không bằng nhau. Giải bài toán trong thời gian $O(\log n)$.

**Phân tích thuật toán:**
Dùng Binary Search. Nếu `nums[mid] < nums[mid + 1]`, điều này có nghĩa là "sườn núi" đang đi lên hướng sang phải, do đó chắc chắn có một đỉnh ở bên phải (bao gồm cả `mid+1`). Nếu ngược lại, đỉnh nằm bên trái (bao gồm `mid`). 

**Mã nguồn Java:**
```java
public class Solution {
    public int findPeakElement(int[] nums) {
        int left = 0;
        int right = nums.length - 1;
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < nums[mid + 1]) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        
        return left;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(\log n)$
- Không gian (Space Complexity): $O(1)$

---

## 3. Find in Mountain Array
**Đề bài chi tiết:**
Cho một API `MountainArray` biểu diễn mảng dạng núi. Cung cấp 2 hàm: `get(k)` trả về phần tử tại index `k`, và `length()` trả về độ dài mảng. Yêu cầu tìm chỉ số nhỏ nhất của phần tử có giá trị bằng `target`. Nếu không tìm thấy, trả về -1. Gọi API `get` tối đa 100 lần.

**Phân tích thuật toán:**
Bài toán chia làm 3 bước gọi Binary Search:
1. Tìm đỉnh của Mountain Array (Tương tự bài Peak Index in a Mountain Array).
2. Tìm `target` trong mảng con tăng dần (từ đầu mảng tới đỉnh).
3. Nếu chưa thấy, tiếp tục tìm `target` trong mảng con giảm dần (từ đỉnh tới cuối mảng).

**Mã nguồn Java:**
```java
// Dummy interface for the sake of code compilation
interface MountainArray {
    public int get(int index);
    public int length();
}

public class Solution {
    public int findInMountainArray(int target, MountainArray mountainArr) {
        int n = mountainArr.length();
        int peakIndex = findPeak(mountainArr, n);
        
        int leftSearch = binarySearch(mountainArr, target, 0, peakIndex, true);
        if (leftSearch != -1) return leftSearch;
        
        return binarySearch(mountainArr, target, peakIndex + 1, n - 1, false);
    }
    
    private int findPeak(MountainArray arr, int n) {
        int left = 0, right = n - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (arr.get(mid) < arr.get(mid + 1)) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left;
    }
    
    private int binarySearch(MountainArray arr, int target, int left, int right, boolean asc) {
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int val = arr.get(mid);
            if (val == target) return mid;
            
            if (asc) {
                if (val < target) left = mid + 1;
                else right = mid - 1;
            } else {
                if (val > target) left = mid + 1;
                else right = mid - 1;
            }
        }
        return -1;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(\log n)$ (3 lần thực hiện Binary Search)
- Không gian: $O(1)$

---

## 4. Median of Two Sorted Arrays
**Đề bài chi tiết:**
Cho hai mảng số nguyên đã sắp xếp `nums1` và `nums2` với kích thước lần lượt là `m` và `n`. Hãy trả về trung vị (median) của hai mảng này với độ phức tạp $O(\log(m+n))$.

**Phân tích thuật toán:**
Dùng kỹ thuật **Partitioning** trên mảng nhỏ hơn. Ta chia hai mảng thành hai nửa trái phải sao cho số lượng phần tử nửa trái bằng số lượng phần tử nửa phải, và phần tử lớn nhất bên trái phải $\le$ phần tử nhỏ nhất bên phải. Từ vị trí chia ở mảng nhỏ hơn, ta tính được vị trí chia ở mảng lớn hơn. Sau đó sử dụng Binary Search trên mảng nhỏ hơn để tìm vách ngăn phù hợp.

**Mã nguồn Java:**
```java
public class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        if (nums1.length > nums2.length) {
            return findMedianSortedArrays(nums2, nums1);
        }
        
        int x = nums1.length;
        int y = nums2.length;
        int low = 0;
        int high = x;
        
        while (low <= high) {
            int partitionX = low + (high - low) / 2;
            int partitionY = (x + y + 1) / 2 - partitionX;
            
            int maxLeftX = (partitionX == 0) ? Integer.MIN_VALUE : nums1[partitionX - 1];
            int minRightX = (partitionX == x) ? Integer.MAX_VALUE : nums1[partitionX];
            
            int maxLeftY = (partitionY == 0) ? Integer.MIN_VALUE : nums2[partitionY - 1];
            int minRightY = (partitionY == y) ? Integer.MAX_VALUE : nums2[partitionY];
            
            if (maxLeftX <= minRightY && maxLeftY <= minRightX) {
                if ((x + y) % 2 == 0) {
                    return ((double)Math.max(maxLeftX, maxLeftY) + Math.min(minRightX, minRightY)) / 2;
                } else {
                    return (double)Math.max(maxLeftX, maxLeftY);
                }
            } else if (maxLeftX > minRightY) {
                high = partitionX - 1;
            } else {
                low = partitionX + 1;
            }
        }
        
        throw new IllegalArgumentException();
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(\log(\min(m, n)))$
- Không gian: $O(1)$

---

## 5. K-th Element of Two Sorted Arrays
**Đề bài chi tiết:**
Cho hai mảng số nguyên đã sắp xếp `arr1` và `arr2` kích thước `m` và `n`. Tìm phần tử nhỏ thứ `k` của cả hai mảng sau khi gộp. Thời gian yêu cầu $O(\log(\min(m, n)))$.

**Phân tích thuật toán:**
Ý tưởng hoàn toàn tương tự Median of Two Sorted Arrays, đây là bài toán tổng quát. Chúng ta cần chia sao cho tổng số phần tử bên trái vách ngăn là `k`. Binary search vách ngăn `partitionX` trên mảng nhỏ hơn, từ đó tính `partitionY = k - partitionX`. Xử lý thêm biên `low` và `high` để `partitionY` không bị âm hay vượt quá `n`.

**Mã nguồn Java:**
```java
public class Solution {
    public int kthElement(int arr1[], int arr2[], int m, int n, int k) {
        if (m > n) {
            return kthElement(arr2, arr1, n, m, k);
        }
        
        // Vì số phần tử bên trái là k, nên partitionX có thể nằm trong [max(0, k-n), min(k, m)]
        int low = Math.max(0, k - n);
        int high = Math.min(m, k);
        
        while (low <= high) {
            int partitionX = low + (high - low) / 2;
            int partitionY = k - partitionX;
            
            int maxLeftX = (partitionX == 0) ? Integer.MIN_VALUE : arr1[partitionX - 1];
            int minRightX = (partitionX == m) ? Integer.MAX_VALUE : arr1[partitionX];
            
            int maxLeftY = (partitionY == 0) ? Integer.MIN_VALUE : arr2[partitionY - 1];
            int minRightY = (partitionY == n) ? Integer.MAX_VALUE : arr2[partitionY];
            
            if (maxLeftX <= minRightY && maxLeftY <= minRightX) {
                return Math.max(maxLeftX, maxLeftY);
            } else if (maxLeftX > minRightY) {
                high = partitionX - 1;
            } else {
                low = partitionX + 1;
            }
        }
        return -1;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(\log(\min(m, n)))$
- Không gian: $O(1)$

---

## 6. Minimum Limit of Balls in a Bag
**Đề bài chi tiết:**
Cho một mảng `nums` mô tả số bóng trong các túi, và một số nguyên `maxOperations`. Trong một thao tác, có thể chia một túi thành hai túi mới có số bóng lớn hơn 0. Tìm giới hạn nhỏ nhất của kích thước lớn nhất của các túi (minimize the maximum number of balls in a bag) sao cho số thao tác $\le$ `maxOperations`.

**Phân tích thuật toán:**
Đây là dạng bài **Binary Search on Answer** kinh điển. Giá trị túi max `mid` dao động từ `1` đến giá trị max trong mảng. Nếu ta chọn một mức chặn là `mid`, số lần chia cần thiết cho 1 túi có size `x` là `(x - 1) / mid`. Tổng số thao tác phải $\le maxOperations$.

**Mã nguồn Java:**
```java
public class Solution {
    public int minimumSize(int[] nums, int maxOperations) {
        int left = 1;
        int right = 0;
        for (int num : nums) {
            right = Math.max(right, num);
        }
        
        int result = right;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (canDivide(nums, maxOperations, mid)) {
                result = mid;
                right = mid - 1; // Try to find a smaller maximum
            } else {
                left = mid + 1;
            }
        }
        
        return result;
    }
    
    private boolean canDivide(int[] nums, int maxOps, int maxLimit) {
        int ops = 0;
        for (int num : nums) {
            ops += (num - 1) / maxLimit;
            if (ops > maxOps) return false;
        }
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log M)$ với $M$ là giá trị lớn nhất trong mảng `nums`.
- Không gian: $O(1)$

---

## 7. Maximum Value at a Given Index in a Bounded Array
**Đề bài chi tiết:**
Bạn cần xây dựng một mảng `nums` kích thước `n` thoả mãn: Tất cả phần tử $> 0$; Chênh lệch 2 phần tử kề nhau $\le 1$; Tổng các phần tử $\le maxSum$. Hãy tìm giá trị lớn nhất có thể của `nums[index]`.

**Phân tích thuật toán:**
Sử dụng **Binary Search on Answer** để dò tìm giá trị của `nums[index]`. Gọi giá trị tại `index` là `mid`. Vì yêu cầu mảng có tổng nhỏ nhất có thể ứng với `mid`, ta phải thiết kế các phần tử hai bên `index` giảm dần thành một mảng Mountain (giảm bậc thang 1 đơn vị, tới 1 thì duy trì 1). Dùng công thức cấp số cộng để tính tổng, tránh tính lặp `O(N)`.

**Mã nguồn Java:**
```java
public class Solution {
    public int maxValue(int n, int index, int maxSum) {
        long left = 1;
        long right = maxSum;
        long ans = 1;
        
        while (left <= right) {
            long mid = left + (right - left) / 2;
            if (getMinSum(n, index, mid) <= maxSum) {
                ans = mid;
                left = mid + 1; // Can we do larger?
            } else {
                right = mid - 1;
            }
        }
        return (int) ans;
    }
    
    private long getMinSum(int n, int index, long val) {
        long sum = val;
        
        // Trái
        long leftCount = index;
        if (val > leftCount) {
            sum += (val - 1 + val - leftCount) * leftCount / 2;
        } else {
            sum += (val - 1 + 1) * (val - 1) / 2 + (leftCount - val + 1);
        }
        
        // Phải
        long rightCount = n - index - 1;
        if (val > rightCount) {
            sum += (val - 1 + val - rightCount) * rightCount / 2;
        } else {
            sum += (val - 1 + 1) * (val - 1) / 2 + (rightCount - val + 1);
        }
        
        return sum;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(\log(\text{maxSum}))$
- Không gian: $O(1)$

---

## 8. Maximum Candies Allocated to K Children
**Đề bài chi tiết:**
Cho mảng `candies` biểu diễn số lượng kẹo ở các đống khác nhau. Có `k` đứa trẻ. Bạn có thể chia nhỏ đống kẹo nhưng không thể ghép chúng lại. Hãy tìm số kẹo lớn nhất mà mỗi đứa trẻ nhận được sao cho mọi đứa trẻ nhận được cùng một số lượng.

**Phân tích thuật toán:**
Dùng **Binary search on answer**. Giả sử mỗi đứa trẻ nhận được `mid` viên kẹo, ta duyệt qua mảng `candies`, số phần kẹo có thể tạo ra từ đống `candies[i]` là `candies[i] / mid`. Nếu tổng số phần kẹo $\ge k$, thì mức `mid` là hợp lệ, ta có thể tăng `mid`. Ngược lại, ta giảm `mid`.

**Mã nguồn Java:**
```java
public class Solution {
    public int maximumCandies(int[] candies, long k) {
        long left = 1;
        long right = 0;
        for (int c : candies) {
            right = Math.max(right, c);
        }
        
        long ans = 0;
        while (left <= right) {
            long mid = left + (right - left) / 2;
            if (canAllocate(candies, k, mid)) {
                ans = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return (int) ans;
    }
    
    private boolean canAllocate(int[] candies, long k, long target) {
        long count = 0;
        for (int c : candies) {
            count += (c / target);
            if (count >= k) return true;
        }
        return false;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log M)$ với $M$ là cực đại kẹo.
- Không gian: $O(1)$

---

## 9. Ternary Search Implementation (Custom Problem)
**Đề bài chi tiết:**
Viết hàm tìm giá trị cực đại của hàm số $f(x) = -x^2 + 10x + 20$ trên đoạn $x \in [-100, 100]$. Sử dụng Ternary Search với độ chính xác `epsilon = 1e-6`. Trả về giá trị của $x$ tại điểm cực đại.

**Phân tích thuật toán:**
Hàm có dạng Parabol úp, do đó là Unimodal (Đơn cực đại). Áp dụng **Ternary Search** với khoảng chia $m1$ và $m2$. Thu hẹp dần cho đến khi `right - left < 1e-6`.

**Mã nguồn Java:**
```java
public class Solution {
    
    // Hàm f(x)
    private double f(double x) {
        return -x * x + 10 * x + 20;
    }
    
    public double findMaxX() {
        double left = -100.0;
        double right = 100.0;
        double epsilon = 1e-6;
        
        while (right - left > epsilon) {
            double m1 = left + (right - left) / 3.0;
            double m2 = right - (right - left) / 3.0;
            
            if (f(m1) < f(m2)) {
                // Điểm cực đại gần m2 hơn, ta vứt nửa trái tới m1
                left = m1;
            } else {
                // Điểm cực đại gần m1 hơn, ta vứt nửa phải sau m2
                right = m2;
            }
        }
        
        return left;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(\log_3 (\frac{\text{right} - \text{left}}{\epsilon}))$, cực kỳ nhanh và hằng số vòng lặp rất nhỏ.
- Không gian: $O(1)$

---

## 10. Find the Duplicate Number (Binary Search on range)
**Đề bài chi tiết:**
Cho mảng `nums` chứa `n + 1` số nguyên, các số đều thuộc đoạn `[1, n]`. Chứng minh rằng ít nhất một số bị lặp lại. Tìm số lặp lại đó mà không sửa đổi mảng và sử dụng không gian bổ sung $O(1)$. (Yêu cầu giải bằng Binary Search để luyện tập, dù Floyd's Tortoise and Hare có $O(N)$).

**Phân tích thuật toán:**
Dùng **Binary Search trên miền giá trị** thay vì chỉ số. Miền giá trị tìm kiếm là `[1, n]`. Gọi một số là `mid`. Ta đếm xem có bao nhiêu số trong mảng nhỏ hơn hoặc bằng `mid`. Theo nguyên lý chuồng bồ câu (Pigeonhole principle), nếu đếm được nhiều hơn `mid` số, số bị lặp phải nằm trong khoảng `[1, mid]`. Ngược lại nằm trong khoảng `[mid + 1, n]`.

**Mã nguồn Java:**
```java
public class Solution {
    public int findDuplicate(int[] nums) {
        int left = 1;
        int right = nums.length - 1; // Tương đương n
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            int count = 0;
            
            for (int num : nums) {
                if (num <= mid) {
                    count++;
                }
            }
            
            if (count > mid) {
                // Số lặp ở nửa dưới
                right = mid;
            } else {
                // Số lặp ở nửa trên
                left = mid + 1;
            }
        }
        return left;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log N)$
- Không gian: $O(1)$

---

## 11. Nth Magical Number
**Đề bài chi tiết:**
Một số dương được gọi là ma thuật (magical) nếu nó chia hết cho `a` hoặc `b`.
Cho ba số nguyên dương `n`, `a`, và `b`, trả về số ma thuật thứ `n`. Vì kết quả có thể rất lớn, trả về phần dư của nó khi chia cho `10^9 + 7`.

**Phân tích thuật toán:**
Sử dụng **Binary Search on Answer**. Hàm đếm số lượng các số ma thuật nhỏ hơn hoặc bằng `mid` là:
`count = mid / a + mid / b - mid / lcm(a, b)`
Trong đó `lcm(a, b)` là bội chung nhỏ nhất của `a` và `b`.
Chúng ta cần tìm giá trị `mid` nhỏ nhất sao cho `count == n`.
Giới hạn tìm kiếm: `left = min(a, b)`, `right = n * min(a, b)`.

**Mã nguồn Java:**
```java
public class Solution {
    public int nthMagicalNumber(int n, int a, int b) {
        long MOD = 1_000_000_007;
        long left = Math.min(a, b);
        long right = (long) n * Math.min(a, b);
        long lcm = (long) a * b / gcd(a, b);
        
        while (left < right) {
            long mid = left + (right - left) / 2;
            long count = (mid / a) + (mid / b) - (mid / lcm);
            
            if (count < n) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        
        return (int) (left % MOD);
    }
    
    private int gcd(int a, int b) {
        if (b == 0) return a;
        return gcd(b, a % b);
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(\log(n \cdot \min(a, b)))$
- Không gian (Space Complexity): $O(1)$

---

## 12. Koko Eating Bananas
**Đề bài chi tiết:**
Koko thích ăn chuối. Có `n` nải chuối, nải thứ `i` có `piles[i]` quả. Có bảo vệ đã đi vắng và sẽ trở lại sau `h` giờ.
Koko quyết định tốc độ ăn chuối của mình là `k` quả mỗi giờ. Mỗi giờ, Koko chọn một nải chuối và ăn `k` quả từ nải đó. Nếu nải chuối có ít hơn `k` quả, Koko sẽ ăn hết nải đó và không ăn thêm quả nào trong giờ đó.
Tìm tốc độ ăn `k` nhỏ nhất sao cho Koko có thể ăn hết tất cả chuối trong vòng `h` giờ.

**Phân tích thuật toán:**
Dùng **Binary Search on Answer**. Tốc độ ăn tối thiểu là 1, tối đa là số lượng chuối lớn nhất trong một nải (vì tốc độ lớn hơn cũng không giúp ăn nhanh hơn do mỗi giờ chỉ ăn một nải).
Với mỗi tốc độ `mid`, tính tổng số giờ cần thiết `hours += (pile + mid - 1) / mid`. Nếu tổng giờ $\le h$, tốc độ này khả thi, thử tìm tốc độ nhỏ hơn.

**Mã nguồn Java:**
```java
public class Solution {
    public int minEatingSpeed(int[] piles, int h) {
        int left = 1;
        int right = 0;
        for (int pile : piles) {
            right = Math.max(right, pile);
        }
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (canEatAll(piles, h, mid)) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        
        return left;
    }
    
    private boolean canEatAll(int[] piles, int h, int k) {
        int hours = 0;
        for (int pile : piles) {
            hours += (pile + k - 1) / k; // Tương đương Math.ceil((double)pile / k)
            if (hours > h) return false;
        }
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log M)$, với $N$ là số nải chuối, $M$ là số chuối lớn nhất trong một nải.
- Không gian: $O(1)$

---

## 13. Capacity To Ship Packages Within D Days
**Đề bài chi tiết:**
Băng chuyền cần vận chuyển các gói hàng có trọng lượng `weights` theo thứ tự cho trước trong vòng `days` ngày.
Mỗi ngày, ta xếp hàng lên tàu sao cho tổng trọng lượng không vượt quá tải trọng tối đa của tàu. Tìm tải trọng tàu nhỏ nhất sao cho có thể vận chuyển tất cả hàng hóa trong vòng `days` ngày.

**Phân tích thuật toán:**
Sử dụng **Binary Search on Answer**. Tải trọng nhỏ nhất có thể là gói hàng lớn nhất (vì không gói nào được chia nhỏ), và tải trọng lớn nhất là tổng tất cả các gói hàng (chở trong 1 ngày).
Với mỗi tải trọng `mid`, ta tính xem cần bao nhiêu ngày bằng cách duyệt qua `weights` và cộng dồn, nếu vượt quá `mid` thì chuyển sang ngày hôm sau.

**Mã nguồn Java:**
```java
public class Solution {
    public int shipWithinDays(int[] weights, int days) {
        int left = 0;
        int right = 0;
        for (int w : weights) {
            left = Math.max(left, w);
            right += w;
        }
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (canShip(weights, days, mid)) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        
        return left;
    }
    
    private boolean canShip(int[] weights, int days, int capacity) {
        int requiredDays = 1;
        int currentWeight = 0;
        
        for (int w : weights) {
            if (currentWeight + w > capacity) {
                requiredDays++;
                currentWeight = w;
                if (requiredDays > days) return false;
            } else {
                currentWeight += w;
            }
        }
        
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log(\sum \text{weights} - \max(\text{weights})))$
- Không gian: $O(1)$

---

## 14. Split Array Largest Sum
**Đề bài chi tiết:**
Cho mảng số nguyên không âm `nums` và một số nguyên `k`. Chia mảng thành `k` mảng con khác rỗng và liên tiếp.
Viết thuật toán cực tiểu hóa tổng lớn nhất trong `k` mảng con đó (Minimize the largest sum).

**Phân tích thuật toán:**
Bài toán này tương đương hoàn toàn với "Capacity To Ship Packages Within D Days".
Biến `nums` thành `weights` và `k` thành `days`. Giới hạn tìm kiếm từ phần tử lớn nhất trong mảng đến tổng toàn mảng. Dùng **Binary Search** để thử các giá trị tổng lớn nhất.

**Mã nguồn Java:**
```java
public class Solution {
    public int splitArray(int[] nums, int k) {
        int left = 0;
        int right = 0;
        for (int num : nums) {
            left = Math.max(left, num);
            right += num;
        }
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (canSplit(nums, k, mid)) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        
        return left;
    }
    
    private boolean canSplit(int[] nums, int k, int maxSum) {
        int parts = 1;
        int currentSum = 0;
        
        for (int num : nums) {
            if (currentSum + num > maxSum) {
                parts++;
                currentSum = num;
                if (parts > k) return false;
            } else {
                currentSum += num;
            }
        }
        
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log(\sum \text{nums} - \max(\text{nums})))$
- Không gian: $O(1)$

---

## 15. Minimum Number of Days to Make m Bouquets
**Đề bài chi tiết:**
Cho mảng `bloomDay`, phần tử thứ `i` là ngày hoa thứ `i` sẽ nở. Cần làm `m` bó hoa, mỗi bó hoa yêu cầu `k` bông hoa **liền kề** nhau.
Tìm số ngày tối thiểu phải chờ để có thể làm được `m` bó hoa. Trả về -1 nếu không thể.

**Phân tích thuật toán:**
Dùng **Binary Search on Answer**. Nếu $m \times k > \text{bloomDay.length}$, ta không bao giờ có đủ hoa, trả về -1.
Miền tìm kiếm ngày là từ 1 đến giá trị `bloomDay` lớn nhất.
Tại một ngày `mid`, ta kiểm tra các hoa đã nở (`bloomDay[i] <= mid`). Đếm các hoa nở liên tiếp, khi đủ `k` hoa thì tạo thành 1 bó và reset biến đếm.

**Mã nguồn Java:**
```java
public class Solution {
    public int minDays(int[] bloomDay, int m, int k) {
        if ((long) m * k > bloomDay.length) return -1;
        
        int left = 1;
        int right = 0;
        for (int day : bloomDay) {
            right = Math.max(right, day);
        }
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (canMakeBouquets(bloomDay, m, k, mid)) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        
        return left;
    }
    
    private boolean canMakeBouquets(int[] bloomDay, int m, int k, int day) {
        int bouquets = 0;
        int flowers = 0;
        
        for (int bDay : bloomDay) {
            if (bDay <= day) {
                flowers++;
                if (flowers == k) {
                    bouquets++;
                    flowers = 0;
                }
            } else {
                flowers = 0;
            }
        }
        
        return bouquets >= m;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log(\max(\text{bloomDay})))$
- Không gian: $O(1)$

---

## 16. Magnetic Force Between Two Balls
**Đề bài chi tiết:**
Trong vũ trụ, có `n` rổ chứa xếp trên một đường thẳng. Rổ thứ `i` ở vị trí `position[i]`. Bạn có `m` quả bóng từ tính và muốn phân phối chúng vào các rổ sao cho lực từ trường nhỏ nhất giữa 2 quả bóng bất kỳ là lớn nhất. Lực từ trường giữa hai quả bóng tại `x` và `y` là `|x - y|`.
Trả về lực từ trường nhỏ nhất có thể lớn nhất (Maximize the minimum distance).

**Phân tích thuật toán:**
Bài toán tìm giá trị "lớn nhất của nhỏ nhất". Sắp xếp mảng `position`.
Sử dụng **Binary Search** trên khoảng cách `mid`. Kiểm tra xem liệu có thể đặt `m` bóng sao cho khoảng cách giữa 2 bóng liên tiếp luôn $\ge mid$. Đặt bóng đầu tiên ở vị trí nhỏ nhất, các bóng sau ưu tiên đặt ở rổ đầu tiên thoả mãn khoảng cách $\ge mid$.

**Mã nguồn Java:**
```java
import java.util.Arrays;

public class Solution {
    public int maxDistance(int[] position, int m) {
        Arrays.sort(position);
        int left = 1;
        int right = position[position.length - 1] - position[0];
        int ans = -1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (canPlaceBalls(position, m, mid)) {
                ans = mid;
                left = mid + 1; // Thử tìm khoảng cách lớn hơn
            } else {
                right = mid - 1;
            }
        }
        
        return ans;
    }
    
    private boolean canPlaceBalls(int[] position, int m, int minDist) {
        int count = 1;
        int lastPos = position[0];
        
        for (int i = 1; i < position.length; i++) {
            if (position[i] - lastPos >= minDist) {
                count++;
                lastPos = position[i];
                if (count == m) return true;
            }
        }
        
        return count >= m;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log N + N \log D)$, với $D = \max(\text{pos}) - \min(\text{pos})$.
- Không gian: $O(1)$ (hoặc $O(\log N)$ để sắp xếp).

---

## 17. Aggressive Cows (SPOJ)
**Đề bài chi tiết:**
Nông dân John có `N` chuồng bò nằm trên một trục tọa độ 1 chiều tại các vị trí `x1, x2, ..., xN`. Ông muốn xếp `C` con bò vào các chuồng sao cho khoảng cách tối thiểu giữa 2 con bò bất kỳ là lớn nhất. (Đây chính là phiên bản gốc của bài Magnetic Force Between Two Balls).

**Phân tích thuật toán:**
Hoàn toàn giống bài **Magnetic Force Between Two Balls**. Đầu tiên cần sắp xếp vị trí chuồng bò. Dùng **Binary Search** để tìm khoảng cách tối thiểu lớn nhất. Khởi tạo `left = 1`, `right = max(x) - min(x)`.

**Mã nguồn Java:**
```java
import java.util.Arrays;

public class Solution {
    public int aggressiveCows(int[] stalls, int k) {
        Arrays.sort(stalls);
        int left = 1;
        int right = stalls[stalls.length - 1] - stalls[0];
        int ans = 1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (canPlaceCows(stalls, k, mid)) {
                ans = mid;
                left = mid + 1; // Tìm khoảng cách lớn hơn
            } else {
                right = mid - 1;
            }
        }
        
        return ans;
    }
    
    private boolean canPlaceCows(int[] stalls, int k, int minDist) {
        int count = 1;
        int lastPos = stalls[0];
        
        for (int i = 1; i < stalls.length; i++) {
            if (stalls[i] - lastPos >= minDist) {
                count++;
                lastPos = stalls[i];
                if (count == k) return true;
            }
        }
        
        return count >= k;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log N + N \log(\max - \min))$
- Không gian: $O(1)$ (Không tính không gian cho sắp xếp)

---

## 18. Kth Smallest Number in Multiplication Table
**Đề bài chi tiết:**
Gần như mọi người đều từng dùng bảng cửu chương. Một bảng cửu chương có kích thước `m x n` chứa các giá trị là tích `i * j` ($1 \le i \le m, 1 \le j \le n$).
Cho `m`, `n` và `k`, trả về phần tử nhỏ thứ `k` trong bảng.

**Phân tích thuật toán:**
Dùng **Binary Search** trên miền giá trị kết quả, vì giá trị chạy từ $1$ tới $m \times n$.
Ở mỗi giá trị `mid`, ta đếm xem có bao nhiêu số trong bảng nhỏ hơn hoặc bằng `mid`. Đối với mỗi hàng `i`, số lượng phần tử $\le mid$ là $\min(mid / i, n)$. Tổng số lượng phần tử $\le mid$ quyết định việc điều chỉnh khoảng tìm kiếm.

**Mã nguồn Java:**
```java
public class Solution {
    public int findKthNumber(int m, int n, int k) {
        int left = 1;
        int right = m * n;
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (countLessEqual(m, n, mid) < k) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        
        return left;
    }
    
    private int countLessEqual(int m, int n, int target) {
        int count = 0;
        for (int i = 1; i <= m; i++) {
            count += Math.min(target / i, n);
        }
        return count;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(m \log(m \cdot n))$ (Có thể tối ưu bằng cách đảm bảo vòng lặp chạy đến $\min(m, n)$)
- Không gian: $O(1)$

---

## 19. Find K-th Smallest Pair Distance
**Đề bài chi tiết:**
Khoảng cách của một cặp số (a, b) được định nghĩa là sự chênh lệch tuyệt đối giữa chúng `|a - b|`. Cho một mảng số nguyên `nums` và số nguyên `k`. Tìm khoảng cách nhỏ thứ `k` trong tất cả các cặp có thể.

**Phân tích thuật toán:**
Sắp xếp mảng `nums`.
Áp dụng **Binary Search** trên giá trị khoảng cách (distance). Miền tìm kiếm từ `0` đến `nums[n-1] - nums[0]`.
Ở mỗi `mid`, dùng kỹ thuật **Sliding Window** (Two Pointers) để đếm số cặp có khoảng cách $\le mid$. Với mỗi `right`, tăng `left` cho đến khi `nums[right] - nums[left] <= mid`. Số cặp hợp lệ kết thúc tại `right` là `right - left`.

**Mã nguồn Java:**
```java
import java.util.Arrays;

public class Solution {
    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums);
        int n = nums.length;
        int left = 0;
        int right = nums[n - 1] - nums[0];
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (countPairs(nums, mid) < k) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        
        return left;
    }
    
    private int countPairs(int[] nums, int mid) {
        int count = 0;
        int left = 0;
        for (int right = 0; right < nums.length; right++) {
            while (nums[right] - nums[left] > mid) {
                left++;
            }
            count += right - left;
        }
        return count;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log N + N \log(\max - \min))$
- Không gian: $O(1)$ (hoặc $O(\log N)$ nếu tính sort)

---

## 20. Minimize Max Distance to Gas Station
**Đề bài chi tiết:**
Bạn có `N` trạm xăng trên một trục hoành, vị trí thứ `i` là `stations[i]`. Bạn được phép thêm `k` trạm xăng mới ở bất kỳ đâu (tọa độ số thực). Nhiệm vụ là tối thiểu hoá khoảng cách lớn nhất giữa hai trạm xăng liên tiếp sau khi thêm `k` trạm xăng.
Kết quả có độ chính xác $10^{-6}$.

**Phân tích thuật toán:**
Dùng **Binary Search** trên số thực.
Khởi tạo `left = 0`, `right = stations[N-1] - stations[0]`.
Với mỗi khoảng cách `mid`, ta tính cần thêm bao nhiêu trạm xăng mới giữa `stations[i]` và `stations[i+1]` để khoảng cách tối đa $\le mid$. Số trạm cần thêm là $\lfloor (stations[i+1] - stations[i]) / mid \rfloor$. Tổng số lượng trạm so sánh với `k`.

**Mã nguồn Java:**
```java
public class Solution {
    public double minmaxGasDist(int[] stations, int k) {
        double left = 0;
        double right = stations[stations.length - 1] - stations[0];
        
        // Điều kiện dừng epsilon 1e-6
        while (right - left > 1e-6) {
            double mid = left + (right - left) / 2.0;
            if (canPlace(stations, k, mid)) {
                right = mid; // Có thể giảm max distance
            } else {
                left = mid;  // Cần khoảng cách lớn hơn
            }
        }
        
        return left;
    }
    
    private boolean canPlace(int[] stations, int k, double mid) {
        int count = 0;
        for (int i = 0; i < stations.length - 1; i++) {
            count += (int) ((stations[i + 1] - stations[i]) / mid);
        }
        return count <= k;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log(\frac{\max - \min}{10^{-6}}))$, vô cùng nhỏ.
- Không gian: $O(1)$

---

## 21. Allocate Minimum Number of Pages
**Đề bài chi tiết:**
Cho một mảng `A` gồm $N$ phần tử, trong đó `A[i]` là số trang của quyển sách thứ $i$. Có $M$ học sinh. Cần phân chia các quyển sách cho $M$ học sinh sao cho:
1. Mỗi học sinh được phân ít nhất 1 quyển sách.
2. Các quyển sách được phân cho mỗi học sinh phải liên tiếp nhau.
Mục tiêu là tìm ra cách phân chia sao cho số lượng trang sách lớn nhất mà một học sinh phải đọc là nhỏ nhất có thể (Minimize the maximum number of pages). Nếu $N < M$, trả về -1 (không thể chia được).

**Phân tích thuật toán:**
Dùng **Binary Search on Answer**. Bài này hoàn toàn giống bài "Capacity To Ship Packages Within D Days" hoặc "Split Array Largest Sum".
Miền tìm kiếm: `left = max(A)`, `right = sum(A)`. Tại mỗi bước `mid`, đếm số học sinh cần thiết để không ai phải đọc nhiều hơn `mid` trang. Nếu số học sinh $\le M$, `mid` có thể là kết quả, tìm nhỏ hơn.

**Mã nguồn Java:**
```java
public class Solution {
    public int findPages(int[] A, int N, int M) {
        if (N < M) return -1;
        
        int left = 0;
        int right = 0;
        for (int pages : A) {
            left = Math.max(left, pages);
            right += pages;
        }
        
        int result = -1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (isPossible(A, M, mid)) {
                result = mid;
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        
        return result;
    }
    
    private boolean isPossible(int[] A, int M, int maxPages) {
        int studentsRequired = 1;
        int currentPages = 0;
        
        for (int pages : A) {
            if (currentPages + pages > maxPages) {
                studentsRequired++;
                currentPages = pages;
                if (studentsRequired > M) return false;
            } else {
                currentPages += pages;
            }
        }
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log(\sum A - \max(A)))$
- Không gian: $O(1)$

---

## 22. H-Index II
**Đề bài chi tiết:**
Cho mảng `citations` chứa số lượng trích dẫn của một nhà nghiên cứu, **đã được sắp xếp tăng dần**. Tìm chỉ số h-index của người đó. H-index là giá trị $h$ lớn nhất sao cho có ít nhất $h$ bài báo có ít nhất $h$ trích dẫn mỗi bài. Yêu cầu giải thuật chạy trong $O(\log n)$.

**Phân tích thuật toán:**
Dùng **Binary Search** trên chỉ số mảng. Với mảng đã sắp xếp độ dài $N$, tại vị trí `mid`, số lượng bài báo có số trích dẫn $\ge \text{citations}[mid]$ là $N - mid$.
Nếu $\text{citations}[mid] \ge N - mid$, ta có thể đạt được H-index ít nhất là $N - mid$. Thử tìm `mid` nhỏ hơn (đi về bên trái) để có số lượng bài báo lớn hơn. Nếu ngược lại, đi về bên phải.

**Mã nguồn Java:**
```java
public class Solution {
    public int hIndex(int[] citations) {
        int n = citations.length;
        int left = 0;
        int right = n - 1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (citations[mid] == n - mid) {
                return n - mid;
            } else if (citations[mid] < n - mid) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        
        return n - left;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(\log N)$
- Không gian: $O(1)$

---

## 23. Search in a Sorted Array of Unknown Size
**Đề bài chi tiết:**
Cho một mảng nguyên được sắp xếp tăng dần không biết trước độ dài. Có một interface `ArrayReader` với hàm `get(k)` trả về phần tử tại index `k`. Nếu index vượt quá độ dài mảng, hàm trả về `2^31 - 1`. Cần tìm kiếm giá trị `target`, trả về index nếu tìm thấy, ngược lại trả về -1.

**Phân tích thuật toán:**
Vì không biết độ dài mảng, ta cần **giới hạn khoảng tìm kiếm (Bounding)** trước.
Bắt đầu với `left = 0`, `right = 1`. Miễn là `reader.get(right) < target`, ta nới rộng bằng cách nhân đôi: `left = right`, `right *= 2`. Sau khi có giới hạn `[left, right]`, áp dụng **Binary Search** bình thường.

**Mã nguồn Java:**
```java
/**
 * // This is ArrayReader's API interface.
 * // You should not implement it, or speculate about its implementation
 * interface ArrayReader {
 *     public int get(int index) {}
 * }
 */
public class Solution {
    public int search(ArrayReader reader, int target) {
        int left = 0;
        int right = 1;
        
        // Mở rộng không gian tìm kiếm theo cấp số nhân
        while (reader.get(right) < target) {
            left = right;
            right <<= 1;
        }
        
        // Binary Search thông thường trên đoạn [left, right]
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int num = reader.get(mid);
            
            if (num == target) {
                return mid;
            } else if (num < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        
        return -1;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(\log T)$, với $T$ là vị trí của phần tử `target`.
- Không gian: $O(1)$

---

## 24. Search a 2D Matrix
**Đề bài chi tiết:**
Cho một ma trận kích thước $m \times n$. Ma trận này có hai tính chất:
1. Các số trên mỗi hàng được sắp xếp tăng dần từ trái qua phải.
2. Số đầu tiên của một hàng lớn hơn số cuối cùng của hàng trước đó.
Kiểm tra xem giá trị `target` có nằm trong ma trận hay không.

**Phân tích thuật toán:**
Vì hàng sau nối tiếp hàng trước và tất cả đều tăng dần, ta có thể tưởng tượng ma trận như một mảng 1D. Số phần tử là $m \times n$.
Áp dụng **Binary Search**, với `left = 0`, `right = m * n - 1`. Chỉ số $mid$ trên mảng 1D sẽ được ánh xạ ngược về 2D bằng toạ độ: `row = mid / n`, `col = mid % n`.

**Mã nguồn Java:**
```java
public class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return false;
        }
        
        int m = matrix.length;
        int n = matrix[0].length;
        
        int left = 0;
        int right = m * n - 1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int midElement = matrix[mid / n][mid % n];
            
            if (midElement == target) {
                return true;
            } else if (midElement < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        
        return false;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(\log(m \cdot n))$
- Không gian: $O(1)$

---

## 25. Search a 2D Matrix II
**Đề bài chi tiết:**
Cho một ma trận kích thước $m \times n$. Các số trên mỗi hàng được sắp xếp tăng dần từ trái sang phải, các số trên mỗi cột được sắp xếp tăng dần từ trên xuống dưới. Tuy nhiên, phần tử đầu tiên của một hàng không nhất thiết lớn hơn phần tử cuối cùng của hàng liền trước. Tìm kiếm xem giá trị `target` có tồn tại trong ma trận không.

**Phân tích thuật toán:**
Không thể coi ma trận là mảng 1D. Ta có thể duyệt từ **góc trên cùng bên phải** (hoặc góc dưới cùng bên trái). Tại vị trí $(row, col) = (0, n - 1)$:
- Nếu $matrix[row][col] == target$, trả về `true`.
- Nếu $matrix[row][col] > target$, vì cột này tăng dần xuống dưới nên toàn bộ các phần tử bên dưới đều lớn hơn `target`, ta loại bỏ cột hiện tại ($col--$).
- Nếu $matrix[row][col] < target$, vì hàng này tăng dần từ trái sang nên toàn bộ các phần tử bên trái đều nhỏ hơn `target`, ta loại bỏ hàng hiện tại ($row++$).

**Mã nguồn Java:**
```java
public class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return false;
        }
        
        int row = 0;
        int col = matrix[0].length - 1;
        
        while (row < matrix.length && col >= 0) {
            if (matrix[row][col] == target) {
                return true;
            } else if (matrix[row][col] > target) {
                col--;
            } else {
                row++;
            }
        }
        
        return false;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(m + n)$ (Mỗi bước loại đi 1 dòng hoặc 1 cột)
- Không gian: $O(1)$

---

## 26. Find a Peak Element II
**Đề bài chi tiết:**
Cho một ma trận 2D `mat` kích thước $m \times n$ không có phần tử nào giống nhau liền kề. Tìm một **đỉnh** bất kỳ trong ma trận, nơi đỉnh là phần tử lớn hơn tất cả 4 phần tử xung quanh (trên, dưới, trái, phải). Trả về mảng chứa toạ độ $[r, c]$ của đỉnh. Cần thuật toán $O(m \log n)$ hoặc $O(n \log m)$.

**Phân tích thuật toán:**
Áp dụng **Binary Search** trên cột.
Với cột ở giữa `mid_col`, tìm phần tử có giá trị lớn nhất trong cột đó, gọi vị trí là `max_row`.
Tiếp theo, so sánh `mat[max_row][mid_col]` với các phần tử bên trái và bên phải (nếu có):
- Nếu lớn hơn cả trái và phải, nó chính là Peak (vì nó đã lớn nhất cột nên lớn hơn phần tử trên và dưới).
- Nếu phần tử bên trái lớn hơn, chắc chắn có đỉnh ở nửa bên trái (di chuyển `right = mid_col - 1`).
- Nếu phần tử bên phải lớn hơn, chắc chắn có đỉnh ở nửa bên phải (di chuyển `left = mid_col + 1`).

**Mã nguồn Java:**
```java
public class Solution {
    public int[] findPeakGrid(int[][] mat) {
        int startCol = 0, endCol = mat[0].length - 1;
        
        while (startCol <= endCol) {
            int midCol = startCol + (endCol - startCol) / 2;
            int maxRow = 0;
            
            // Tìm Max trong cột hiện tại
            for (int i = 0; i < mat.length; i++) {
                if (mat[i][midCol] > mat[maxRow][midCol]) {
                    maxRow = i;
                }
            }
            
            boolean isLeftBig = midCol - 1 >= 0 && mat[maxRow][midCol - 1] > mat[maxRow][midCol];
            boolean isRightBig = midCol + 1 < mat[0].length && mat[maxRow][midCol + 1] > mat[maxRow][midCol];
            
            if (!isLeftBig && !isRightBig) {
                return new int[]{maxRow, midCol}; // Đạt đỉnh
            } else if (isRightBig) {
                startCol = midCol + 1; // Sườn núi đi lên bên phải
            } else {
                endCol = midCol - 1;   // Sườn núi đi lên bên trái
            }
        }
        return new int[]{-1, -1};
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(m \log n)$ (Tìm cột bằng Binary Search, với mỗi cột mất $O(m)$ tìm Max).
- Không gian: $O(1)$

---

## 27. Restoring the Array From Adjacent Pairs
**Đề bài chi tiết:**
Có một mảng `nums` chứa `n` số nguyên duy nhất. Tuy nhiên, mảng này đã bị phá huỷ và bạn chỉ còn các cặp phần tử liền kề `adjacentPairs`. Mỗi cặp đại diện cho hai số đứng cạnh nhau trong mảng ban đầu, nhưng thứ tự trong cặp không đảm bảo. Khôi phục lại mảng `nums`. 

**Phân tích thuật toán:**
Dù đây là một bài liên quan tới **Đồ thị (Graph)** hay **Hash Map**, ta có thể đặt nó vào lớp mở rộng liên quan tới tìm kiếm. Coi các cặp như các cạnh của đồ thị.
Đỉnh đầu và đỉnh cuối của mảng ban đầu sẽ chỉ có bậc 1 (chỉ kề với 1 đỉnh khác). Các đỉnh ở giữa có bậc 2.
Ta dùng `HashMap` dựng danh sách kề, tìm một đỉnh có bậc 1 để làm điểm xuất phát. Sau đó liên tục tìm kiếm và đi theo hàng xóm chưa được duyệt.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public int[] restoreArray(int[][] adjacentPairs) {
        Map<Integer, List<Integer>> graph = new HashMap<>();
        
        for (int[] edge : adjacentPairs) {
            graph.computeIfAbsent(edge[0], k -> new ArrayList<>()).add(edge[1]);
            graph.computeIfAbsent(edge[1], k -> new ArrayList<>()).add(edge[0]);
        }
        
        int root = 0;
        for (int key : graph.keySet()) {
            if (graph.get(key).size() == 1) {
                root = key; // Điểm bắt đầu
                break;
            }
        }
        
        int n = adjacentPairs.length + 1;
        int[] res = new int[n];
        res[0] = root;
        res[1] = graph.get(root).get(0);
        
        for (int i = 2; i < n; i++) {
            List<Integer> neighbors = graph.get(res[i - 1]);
            res[i] = (neighbors.get(0) == res[i - 2]) ? neighbors.get(1) : neighbors.get(0);
        }
        
        return res;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N)$
- Không gian: $O(N)$ cho HashMap lưu trữ.

---

## 28. Codeforces - Weakness and Poorness
**Đề bài chi tiết:**
Cho mảng `a` có `n` phần tử. "Poorness" của mảng đối với giá trị `x` là giá trị tuyệt đối lớn nhất của tổng mảng con sau khi lấy mỗi phần tử trừ đi `x` (tức là $a_i - x$).
Viết chương trình tìm giá trị `x` (có thể là số thực) sao cho poorness là nhỏ nhất có thể, và trả về poorness tối thiểu đó.

**Phân tích thuật toán:**
Hàm `f(x)` trả về poorness tương ứng với giá trị trừ đi `x`. Hàm này có dạng hình võng (convex - có 1 điểm cực tiểu duy nhất).
Ta có thể dùng **Ternary Search** trên tập số thực `x` trong khoảng `[-10000, 10000]`.
Với mỗi `x`, để tính poorness, ta dùng thuật toán **Kadane** để tìm tổng mảng con lớn nhất và nhỏ nhất của mảng `a_i - x`. Lấy giá trị lớn nhất tuyệt đối.

**Mã nguồn Java:**
```java
public class Solution {
    public double minPoorness(int[] a) {
        double left = -10000.0, right = 10000.0;
        for (int i = 0; i < 100; i++) { // Lặp 100 lần thay cho epsilon
            double m1 = left + (right - left) / 3;
            double m2 = right - (right - left) / 3;
            if (getPoorness(a, m1) < getPoorness(a, m2)) {
                right = m2; // Điểm cực tiểu gần m1 hơn, vứt bên phải m2
            } else {
                left = m1;  // Điểm cực tiểu gần m2 hơn, vứt bên trái m1
            }
        }
        return getPoorness(a, left);
    }
    
    private double getPoorness(int[] a, double x) {
        double maxEndingHere = 0, minEndingHere = 0;
        double maxSoFar = 0, minSoFar = 0;
        
        for (int num : a) {
            double val = num - x;
            maxEndingHere = Math.max(0, maxEndingHere + val);
            maxSoFar = Math.max(maxSoFar, maxEndingHere);
            
            minEndingHere = Math.min(0, minEndingHere + val);
            minSoFar = Math.min(minSoFar, minEndingHere);
        }
        return Math.max(maxSoFar, Math.abs(minSoFar));
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \cdot K)$, với $K$ là hằng số lặp (100-200 vòng).
- Không gian: $O(1)$

---

## 29. Codeforces - Is This JEE?
**Đề bài chi tiết:**
Cho hàm số $f(x) = \frac{x^2 + b \cdot x + c}{\sin(x)}$ trên khoảng $x \in (0, \pi/2)$. Cần tìm cực tiểu của hàm số. Do đặc tính của tử số và mẫu số trên đoạn này, hàm có 1 cực tiểu duy nhất (unimodal). Hãy dùng thuật toán tìm kiếm để giải quyết.

**Phân tích thuật toán:**
Áp dụng **Ternary Search** trên khoảng số thực `(0, \pi / 2)`. Tính giá trị hàm tại hai điểm chia 3. Thu hẹp khoảng dần dần. Thuật toán sẽ dừng khi chênh lệch `right - left` đủ nhỏ (ví dụ $10^{-6}$) hoặc cố định vòng lặp 100 lần.

**Mã nguồn Java:**
```java
public class Solution {
    private double f(double x, double b, double c) {
        return (x * x + b * x + c) / Math.sin(x);
    }
    
    public double getMinimum(double b, double c) {
        double left = 0.000001; // Gần 0
        double right = Math.PI / 2.0 - 0.000001; // Gần PI/2
        
        for (int i = 0; i < 100; i++) {
            double m1 = left + (right - left) / 3.0;
            double m2 = right - (right - left) / 3.0;
            
            if (f(m1, b, c) < f(m2, b, c)) {
                right = m2; // Cực tiểu gần m1, vứt phải
            } else {
                left = m1;  // Cực tiểu gần m2, vứt trái
            }
        }
        return f(left, b, c);
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(K)$, với $K$ là số lần lặp.
- Không gian: $O(1)$

---

## 30. Design a Time-Based Key-Value Store
**Đề bài chi tiết:**
Thiết kế một cấu trúc dữ liệu lưu trữ các giá trị theo thời gian. Có các hàm:
- `set(String key, String value, int timestamp)`: Lưu khoá với giá trị `value` tại thời điểm `timestamp`. (Timestamp luôn tăng dần với mỗi lần gọi hàm).
- `get(String key, int timestamp)`: Trả về giá trị `value` được thiết lập ở thời gian `timestamp_prev` gần nhất thoả mãn `timestamp_prev <= timestamp`. Nếu không có, trả về chuỗi rỗng `""`.

**Phân tích thuật toán:**
Sử dụng `HashMap` trong đó khoá là `key` và giá trị là một `List` chứa các object Pair gồm `(timestamp, value)`.
Vì các `timestamp` được đẩy vào list liên tục và luôn lớn hơn trước, list này **được tự động sắp xếp**. Khi gọi `get()`, ta dùng **Binary Search** trên List để tìm `timestamp` lớn nhất nhưng không vượt quá `target_timestamp`. Giống với tìm `floor` hoặc lower bound.

**Mã nguồn Java:**
```java
import java.util.*;

class Pair {
    int timestamp;
    String value;
    public Pair(int timestamp, String value) {
        this.timestamp = timestamp;
        this.value = value;
    }
}

public class TimeMap {
    private Map<String, List<Pair>> map;

    public TimeMap() {
        map = new HashMap<>();
    }
    
    public void set(String key, String value, int timestamp) {
        map.computeIfAbsent(key, k -> new ArrayList<>()).add(new Pair(timestamp, value));
    }
    
    public String get(String key, int timestamp) {
        if (!map.containsKey(key)) return "";
        
        List<Pair> list = map.get(key);
        int left = 0;
        int right = list.size() - 1;
        String res = "";
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (list.get(mid).timestamp <= timestamp) {
                res = list.get(mid).value;
                left = mid + 1; // Thử tìm gần hơn về bên phải
            } else {
                right = mid - 1; // Thời gian đã vượt quá, phải lùi lại
            }
        }
        
        return res;
    }
}
```

**Độ phức tạp:**
- `set`: Thời gian $O(1)$ (Amortized ArrayList append), Không gian tổng cộng $O(N)$
- `get`: Thời gian $O(\log N)$ nhờ Binary Search, với $N$ là số lượng timestamp tương ứng của khoá. Không gian $O(1)$.
