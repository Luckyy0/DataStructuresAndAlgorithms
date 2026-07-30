# Bài tập: Binary Search on Rotated Arrays

## 1. Find Minimum in Rotated Sorted Array
**Đề bài chi tiết**: Cho một mảng `nums` có kích thước `n` ban đầu được sắp xếp tăng dần, sau đó bị xoay (rotated) tại một điểm chưa biết từ 1 đến `n` lần. Hãy tìm phần tử nhỏ nhất trong mảng. Mảng các phần tử là duy nhất. Thuật toán cần chạy trong thời gian $O(\log n)$.
**Phân tích thuật toán**: Dùng Binary Search, so sánh `nums[mid]` với `nums[right]`. Nếu `nums[mid] > nums[right]`, phần tử nhỏ nhất nằm bên phải của `mid`. Ngược lại, phần tử nhỏ nhất nằm từ `mid` trở về trái.
**Mã nguồn Java**:
```java
class Solution {
    public int findMin(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[right]) {
                left = mid + 1; // Nhỏ nhất ở nửa phải
            } else {
                right = mid; // Nhỏ nhất nằm ở nửa trái (bao gồm cả mid)
            }
        }
        return nums[left];
    }
}
```
**Độ phức tạp**: Thời gian $O(\log N)$, Không gian $O(1)$.

---

## 2. Find Minimum in Rotated Sorted Array II (with duplicates)
**Đề bài chi tiết**: Tương tự bài 1, nhưng mảng `nums` có thể chứa các phần tử trùng lặp. Hãy tìm phần tử nhỏ nhất.
**Phân tích thuật toán**: Logic tương tự bài 1, nhưng ta cần xử lý trường hợp `nums[mid] == nums[right]`. Vì ta không biết min nằm ở nửa nào, ta chỉ có thể loại bỏ `right` một cách an toàn bằng `right--`.
**Mã nguồn Java**:
```java
class Solution {
    public int findMin(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[right]) {
                left = mid + 1;
            } else if (nums[mid] < nums[right]) {
                right = mid;
            } else {
                right--; // Xử lý trùng lặp
            }
        }
        return nums[left];
    }
}
```
**Độ phức tạp**: Thời gian trung bình $O(\log N)$, Tệ nhất $O(N)$ (khi các phần tử đều bằng nhau). Không gian $O(1)$.

---

## 3. Search in Rotated Sorted Array
**Đề bài chi tiết**: Cho mảng đã sắp xếp và xoay vòng `nums` không chứa trùng lặp và một số nguyên `target`. Hãy trả về chỉ số (index) của `target` trong mảng, hoặc `-1` nếu không tồn tại. Yêu cầu $O(\log n)$.
**Phân tích thuật toán**: Luôn có ít nhất một nửa mảng đã được sắp xếp. Ta sẽ kiểm tra nửa nào được sắp xếp, và xem `target` có nằm trong vùng biên của nửa đó không, để tiếp tục chặt nhị phân.
**Mã nguồn Java**:
```java
class Solution {
    public int search(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) return mid;
            
            // Nửa trái đã sắp xếp
            if (nums[left] <= nums[mid]) {
                if (target >= nums[left] && target < nums[mid]) right = mid - 1;
                else left = mid + 1;
            } 
            // Nửa phải đã sắp xếp
            else {
                if (target > nums[mid] && target <= nums[right]) left = mid + 1;
                else right = mid - 1;
            }
        }
        return -1;
    }
}
```
**Độ phức tạp**: Thời gian $O(\log N)$, Không gian $O(1)$.

---

## 4. Search in Rotated Sorted Array II (with duplicates)
**Đề bài chi tiết**: Tương tự bài 3, nhưng mảng `nums` có chứa phần tử trùng lặp. Hãy trả về `true` nếu `target` tồn tại, ngược lại `false`.
**Phân tích thuật toán**: Do có phần tử trùng lặp, ta có thể gặp `nums[left] == nums[mid] == nums[right]`. Lúc này không thể phân biệt nửa nào là đã sắp xếp. Ta phải tăng `left` và giảm `right` để loại bỏ các điểm mập mờ này.
**Mã nguồn Java**:
```java
class Solution {
    public boolean search(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) return true;
            
            // Xử lý trùng lặp 2 đầu
            if (nums[left] == nums[mid] && nums[mid] == nums[right]) {
                left++; right--; continue;
            }
            
            if (nums[left] <= nums[mid]) {
                if (target >= nums[left] && target < nums[mid]) right = mid - 1;
                else left = mid + 1;
            } else {
                if (target > nums[mid] && target <= nums[right]) left = mid + 1;
                else right = mid - 1;
            }
        }
        return false;
    }
}
```
**Độ phức tạp**: Thời gian TB $O(\log N)$, Tệ nhất $O(N)$. Không gian $O(1)$.

---

## 5. Find Rotation Count in Rotated Sorted Array
**Đề bài chi tiết**: Cho mảng đã sắp xếp gồm các số phân biệt bị xoay. Hãy tìm số lần mảng bị xoay. (Số lần xoay thực chất là index của phần tử nhỏ nhất).
**Phân tích thuật toán**: Sử dụng cùng logic với việc tìm phần tử nhỏ nhất. Thay vì trả về giá trị, ta trả về index.
**Mã nguồn Java**:
```java
class Solution {
    public int findRotationCount(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[right]) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left; // Index của giá trị nhỏ nhất chính là số lần rotate
    }
}
```
**Độ phức tạp**: Thời gian $O(\log N)$, Không gian $O(1)$.

---

## 6. Minimum Number of Days to Make m Bouquets
**Đề bài chi tiết**: Cho mảng số nguyên `bloomDay`, số nguyên `m` và `k`. Bạn cần làm `m` bó hoa, mỗi bó gồm `k` bông hoa liền kề. Trả về số ngày tối thiểu để hoàn thành, hoặc `-1` nếu không thể.
**Phân tích thuật toán**: Dùng Binary Search trên khoảng giá trị của số ngày (từ 1 đến max(bloomDay)). Logic kiểm tra (hàm `check`) duyệt mảng để xem có thể tạo đủ bó hoa với `mid` ngày hay không. Liên quan gián tiếp tới pivot logic vì ta tìm điểm "nhảy" từ không thể sang có thể.
**Mã nguồn Java**:
```java
class Solution {
    public int minDays(int[] bloomDay, int m, int k) {
        if ((long) m * k > bloomDay.length) return -1;
        int left = 1, right = (int) 1e9;
        while (left < right) {
            int mid = left + (right - left) / 2;
            int bouquets = 0, flowers = 0;
            for (int day : bloomDay) {
                if (day <= mid) {
                    flowers++;
                    if (flowers == k) { bouquets++; flowers = 0; }
                } else {
                    flowers = 0;
                }
            }
            if (bouquets >= m) right = mid;
            else left = mid + 1;
        }
        return left;
    }
}
```
**Độ phức tạp**: Thời gian $O(N \log (\max(bloomDay)))$, Không gian $O(1)$.

---

## 7. H-Index II
**Đề bài chi tiết**: Cho mảng `citations` chứa số trích dẫn của nhà nghiên cứu, đã được sắp xếp tăng dần. Hãy tính H-index của họ trong $O(\log n)$.
**Phân tích thuật toán**: Ta cần tìm chỉ số `i` sao cho `citations[mid] >= n - mid`. `n - mid` chính là số bài báo có trích dẫn lớn hơn hoặc bằng `citations[mid]`.
**Mã nguồn Java**:
```java
class Solution {
    public int hIndex(int[] citations) {
        int n = citations.length;
        int left = 0, right = n - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (citations[mid] == n - mid) return n - mid;
            else if (citations[mid] < n - mid) left = mid + 1;
            else right = mid - 1;
        }
        return n - left;
    }
}
```
**Độ phức tạp**: Thời gian $O(\log N)$, Không gian $O(1)$.

---

## 8. Single Element in a Sorted Array
**Đề bài chi tiết**: Cho một mảng đã sắp xếp, tất cả các phần tử xuất hiện đúng 2 lần trừ một phần tử xuất hiện 1 lần. Hãy tìm phần tử đó trong $O(\log n)$.
**Phân tích thuật toán**: Số cặp hợp lệ đứt gãy tại vị trí chứa số đơn. Ta kiểm tra index `mid`, ép nó về số chẵn. Nếu `nums[mid] == nums[mid+1]`, tức là phần trước đó hợp lệ, số cần tìm nằm ở nửa phải.
**Mã nguồn Java**:
```java
class Solution {
    public int singleNonDuplicate(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (mid % 2 == 1) mid--; // Đưa mid về index chẵn
            
            if (nums[mid] == nums[mid + 1]) {
                left = mid + 2; // Cặp hợp lệ, đáp án ở bên phải
            } else {
                right = mid; // Lỗi đứt đoạn, đáp án nằm từ mid trở về trái
            }
        }
        return nums[left];
    }
}
```
**Độ phức tạp**: Thời gian $O(\log N)$, Không gian $O(1)$.

---

## 9. Find Right Interval
**Đề bài chi tiết**: Cho mảng các khoảng `intervals` ($start_i$, $end_i$). Với mỗi khoảng `i`, tìm chỉ số `j` sao cho $start_j \ge end_i$ và $start_j$ là nhỏ nhất có thể. Trả về mảng các chỉ số.
**Phân tích thuật toán**: Ta lưu index gốc, sau đó sắp xếp các `intervals` dựa vào điểm `start`. Đối với mỗi khoảng, lấy điểm `end` và dùng Binary Search để tìm điểm `start` nhỏ nhất thỏa mãn.
**Mã nguồn Java**:
```java
import java.util.Arrays;

class Solution {
    public int[] findRightInterval(int[][] intervals) {
        int n = intervals.length;
        int[][] starts = new int[n][2];
        for (int i = 0; i < n; i++) {
            starts[i][0] = intervals[i][0];
            starts[i][1] = i;
        }
        Arrays.sort(starts, (a, b) -> Integer.compare(a[0], b[0]));
        
        int[] res = new int[n];
        for (int i = 0; i < n; i++) {
            int target = intervals[i][1];
            int left = 0, right = n - 1;
            int ans = -1;
            while (left <= right) {
                int mid = left + (right - left) / 2;
                if (starts[mid][0] >= target) {
                    ans = starts[mid][1]; // Cập nhật ứng viên
                    right = mid - 1; // Tìm tiếp bên trái xem có cái nào sát target hơn không
                } else {
                    left = mid + 1;
                }
            }
            res[i] = ans;
        }
        return res;
    }
}
```
**Độ phức tạp**: Thời gian $O(N \log N)$, Không gian $O(N)$.

---

## 10. Two Sum II - Input array is sorted (Binary search approach)
**Đề bài chi tiết**: Cho mảng số nguyên được sắp xếp tăng dần và một số `target`. Hãy tìm hai số có tổng bằng `target`. Trả về mảng chứa chỉ số của chúng (1-indexed).
**Phân tích thuật toán**: Dùng kỹ thuật Two Pointers rất phù hợp với bài này, di chuyển `left` và `right`. Kỹ thuật này bản chất cũng thu hẹp không gian tìm kiếm tương tự như cấu trúc logic của Binary Search.
**Mã nguồn Java**:
```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0, right = numbers.length - 1;
        while (left < right) {
            int sum = numbers[left] + numbers[right];
            if (sum == target) {
                return new int[]{left + 1, right + 1};
            } else if (sum < target) {
                left++;
            } else {
                right--;
            }
        }
        return new int[]{-1, -1};
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(1)$.

---

## 11. Search in Rotated Sorted Array III (2D Grid)
**Đề bài chi tiết**: Cho một ma trận 2D kích thước `m x n`, mỗi hàng được sắp xếp tăng dần từ trái sang phải. Giả sử ma trận này được trải phẳng thành mảng 1D và sau đó bị xoay vòng (rotated) tại một điểm chưa biết. Hãy tìm kiếm số `target` trong ma trận 2D này. Trả về `true` nếu `target` tồn tại, ngược lại trả về `false`.
**Phân tích thuật toán**: Ta có thể ánh xạ (map) ma trận 2D thành một mảng 1D ảo với các chỉ số từ $0$ đến $m \times n - 1$. Với một chỉ số `mid`, tọa độ 2D tương ứng là `row = mid / n` và `col = mid % n`. Từ đó, ta có thể áp dụng thuật toán tìm kiếm Binary Search trên mảng xoay vòng bình thường.
**Mã nguồn Java**:
```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return false;
        int m = matrix.length, n = matrix[0].length;
        int left = 0, right = m * n - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int midVal = matrix[mid / n][mid % n];
            int leftVal = matrix[left / n][left % n];
            int rightVal = matrix[right / n][right % n];
            
            if (midVal == target) return true;
            
            // Nửa trái đã được sắp xếp
            if (leftVal <= midVal) {
                if (target >= leftVal && target < midVal) right = mid - 1;
                else left = mid + 1;
            } 
            // Nửa phải đã được sắp xếp
            else {
                if (target > midVal && target <= rightVal) left = mid + 1;
                else right = mid - 1;
            }
        }
        return false;
    }
}
```
**Độ phức tạp**: Thời gian $O(\log (m \times n))$, Không gian $O(1)$.

---

## 12. K-th Smallest in Rotated Array
**Đề bài chi tiết**: Cho mảng đã sắp xếp và xoay vòng `nums`, không chứa phần tử trùng lặp. Hãy tìm phần tử nhỏ thứ `k` trong mảng ($1 \le k \le n$).
**Phân tích thuật toán**: Vì mảng bị xoay vòng, phần tử nhỏ nhất có thể không nằm ở đầu mảng. Đầu tiên, ta sử dụng Binary Search để tìm vị trí của phần tử nhỏ nhất (tức là số lần xoay). Khi đã có vị trí `pivot`, phần tử nhỏ thứ `k` sẽ nằm ở vị trí `(pivot + k - 1) % n`.
**Mã nguồn Java**:
```java
class Solution {
    public int kthSmallest(int[] nums, int k) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[right]) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        int pivot = left; // Chỉ số của phần tử nhỏ nhất
        return nums[(pivot + k - 1) % nums.length];
    }
}
```
**Độ phức tạp**: Thời gian $O(\log N)$ để tìm pivot, $O(1)$ để truy xuất. Tổng thời gian $O(\log N)$, Không gian $O(1)$.

---

## 13. Median of Two Sorted Arrays
**Đề bài chi tiết**: Cho hai mảng số nguyên đã sắp xếp `nums1` và `nums2` có kích thước lần lượt là `m` và `n`. Hãy trả về trung vị (median) của hai mảng này. Yêu cầu độ phức tạp thời gian phải là $O(\log(m+n))$.
**Phân tích thuật toán**: Ta sử dụng Binary Search trên mảng nhỏ hơn (để tối ưu) nhằm phân chia hai mảng thành hai nửa sao cho số lượng phần tử ở nửa trái bằng hoặc lớn hơn nửa phải đúng 1 phần tử. Sau đó kiểm tra chéo: phần tử lớn nhất bên trái phải nhỏ hơn hoặc bằng phần tử nhỏ nhất bên phải. Dựa trên tổng số lượng phần tử chẵn hay lẻ để lấy trung bình hoặc phần tử lớn nhất phía trái.
**Mã nguồn Java**:
```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        if (nums1.length > nums2.length) {
            return findMedianSortedArrays(nums2, nums1);
        }
        int x = nums1.length, y = nums2.length;
        int low = 0, high = x;
        while (low <= high) {
            int partitionX = (low + high) / 2;
            int partitionY = (x + y + 1) / 2 - partitionX;
            
            int maxLeftX = (partitionX == 0) ? Integer.MIN_VALUE : nums1[partitionX - 1];
            int minRightX = (partitionX == x) ? Integer.MAX_VALUE : nums1[partitionX];
            
            int maxLeftY = (partitionY == 0) ? Integer.MIN_VALUE : nums2[partitionY - 1];
            int minRightY = (partitionY == y) ? Integer.MAX_VALUE : nums2[partitionY];
            
            if (maxLeftX <= minRightY && maxLeftY <= minRightX) {
                if ((x + y) % 2 == 0) {
                    return ((double) Math.max(maxLeftX, maxLeftY) + Math.min(minRightX, minRightY)) / 2;
                } else {
                    return (double) Math.max(maxLeftX, maxLeftY);
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
**Độ phức tạp**: Thời gian $O(\log (\min(M, N)))$, Không gian $O(1)$.

---

## 14. Peak Index in a Mountain Array
**Đề bài chi tiết**: Cho mảng số nguyên `arr` đại diện cho một ngọn núi (nghĩa là giá trị tăng dần đến một điểm đỉnh sau đó giảm dần). Hãy tìm chỉ số của đỉnh núi đó, nơi `arr[i - 1] < arr[i] > arr[i + 1]`. Yêu cầu thời gian $O(\log n)$.
**Phân tích thuật toán**: Vì mảng có tính chất tăng dần rồi giảm dần, ta có thể dùng Binary Search. Ta so sánh `arr[mid]` với `arr[mid + 1]`. Nếu `arr[mid] < arr[mid + 1]`, ta vẫn đang leo lên dốc (đỉnh nằm bên phải). Ngược lại, ta đã vượt đỉnh hoặc đang ở đỉnh, nên đỉnh nằm từ `mid` trở về trái.
**Mã nguồn Java**:
```java
class Solution {
    public int peakIndexInMountainArray(int[] arr) {
        int left = 0, right = arr.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (arr[mid] < arr[mid + 1]) {
                left = mid + 1; // Đang đi lên dốc
            } else {
                right = mid; // Đang đi xuống dốc hoặc ngay tại đỉnh
            }
        }
        return left;
    }
}
```
**Độ phức tạp**: Thời gian $O(\log N)$, Không gian $O(1)$.

---

## 15. Find Peak Element
**Đề bài chi tiết**: Cho mảng số nguyên `nums`. Một phần tử được gọi là đỉnh (peak) nếu nó lớn hơn hẳn hai phần tử kề cạnh nó. Hãy trả về chỉ số của bất kỳ đỉnh cục bộ nào trong mảng. Coi như `nums[-1] = nums[n] = -\infty`. Mảng có thể chứa nhiều đỉnh, trả về đỉnh nào cũng được. Yêu cầu $O(\log n)$.
**Phân tích thuật toán**: Tương tự bài đỉnh núi, khi ta luôn đi về phía ngọn dốc (hướng mà phần tử kề lớn hơn), ta chắc chắn sẽ đụng được ít nhất một đỉnh cục bộ, bởi vì 2 đầu mảng là $-\infty$.
**Mã nguồn Java**:
```java
class Solution {
    public int findPeakElement(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < nums[mid + 1]) {
                left = mid + 1; // Đỉnh chắc chắn nằm ở nửa phải
            } else {
                right = mid; // Đỉnh có thể là mid hoặc nằm ở nửa trái
            }
        }
        return left;
    }
}
```
**Độ phức tạp**: Thời gian $O(\log N)$, Không gian $O(1)$.

---

## 16. Check if Array Is Sorted and Rotated
**Đề bài chi tiết**: Cho một mảng `nums`. Hãy trả về `true` nếu mảng này có thể được tạo ra bằng cách lấy một mảng được sắp xếp tăng dần và xoay vòng một số lần (có thể là $0$ lần). Trả về `false` nếu không thể. Mảng có thể chứa phần tử trùng lặp.
**Phân tích thuật toán**: Mảng thỏa mãn yêu cầu khi có tối đa một đứt gãy (vị trí mà `nums[i] > nums[i+1]`). Đừng quên kiểm tra tính vòng lặp: so sánh cả phần tử cuối với phần tử đầu tiên của mảng. Có thể kiểm tra bằng một vòng lặp đơn tuyến tính.
**Mã nguồn Java**:
```java
class Solution {
    public boolean check(int[] nums) {
        int count = 0;
        int n = nums.length;
        for (int i = 0; i < n; i++) {
            // So sánh phần tử hiện tại với phần tử kế tiếp (theo chu kỳ vòng lặp)
            if (nums[i] > nums[(i + 1) % n]) {
                count++;
            }
        }
        return count <= 1;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(1)$.

---

## 17. Minimum Number of Rotations to Recover Original Array
**Đề bài chi tiết**: Mảng `nums` ban đầu được sắp xếp tăng dần (các phần tử là duy nhất) nhưng bị xoay vòng. Tìm số lần dịch chuyển sang phải (rotations) tối thiểu để khôi phục mảng về trạng thái sắp xếp ban đầu.
**Phân tích thuật toán**: Số lần mảng bị xoay sang phải thực chất bằng chính chỉ số (index) của phần tử nhỏ nhất trong mảng (điểm bắt đầu của mảng gốc). Ta dùng Binary Search để tìm vị trí của phần tử này.
**Mã nguồn Java**:
```java
class Solution {
    public int minimumRotations(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[right]) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left; // Index của min chính là số bước cần xoay
    }
}
```
**Độ phức tạp**: Thời gian $O(\log N)$, Không gian $O(1)$.

---

## 18. Find First and Last Position of Element in Sorted Array
**Đề bài chi tiết**: Cho mảng số nguyên `nums` được sắp xếp tăng dần và một số nguyên `target`. Hãy tìm vị trí xuất hiện đầu tiên và cuối cùng của `target` trong mảng. Nếu không tìm thấy, trả về `[-1, -1]`. Thuật toán phải chạy trong thời gian $O(\log n)$.
**Phân tích thuật toán**: Áp dụng Binary Search hai lần. Lần thứ nhất tìm vị trí đầu tiên (di chuyển `right` về `mid` ngay cả khi `nums[mid] == target`). Lần thứ hai tìm vị trí cuối cùng (di chuyển `left` lên `mid` khi `nums[mid] == target`, lúc này cần tính `mid = left + (right - left + 1) / 2` để tránh bị kẹp).
**Mã nguồn Java**:
```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int[] result = new int[]{-1, -1};
        if (nums == null || nums.length == 0) return result;
        
        // Tìm vị trí đầu tiên
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] >= target) right = mid;
            else left = mid + 1;
        }
        if (nums[left] != target) return result;
        result[0] = left;
        
        // Tìm vị trí cuối cùng
        right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left + 1) / 2; // +1 để làm tròn lên, tránh vòng lặp vô hạn
            if (nums[mid] <= target) left = mid;
            else right = mid - 1;
        }
        result[1] = right; // Trả về right (hay left đều như nhau)
        
        return result;
    }
}
```
**Độ phức tạp**: Thời gian $O(\log N)$, Không gian $O(1)$.

---

## 19. Valid Perfect Square
**Đề bài chi tiết**: Cho số nguyên dương `num`. Trả về `true` nếu `num` là một số chính phương, ngược lại trả về `false`. Đặc biệt yêu cầu không sử dụng các hàm toán học tích hợp sẵn (như `sqrt`). Dùng Binary Search để tìm nghiệm.
**Phân tích thuật toán**: Không gian tìm kiếm là các số từ $1$ đến `num`. Tại mỗi bước chia đôi, tính bình phương của `mid`. Cần sử dụng kiểu `long` để tránh tràn số học (integer overflow) khi nhân.
**Mã nguồn Java**:
```java
class Solution {
    public boolean isPerfectSquare(int num) {
        if (num < 1) return false;
        long left = 1, right = num;
        while (left <= right) {
            long mid = left + (right - left) / 2;
            long square = mid * mid;
            
            if (square == num) return true;
            else if (square < num) left = mid + 1;
            else right = mid - 1;
        }
        return false;
    }
}
```
**Độ phức tạp**: Thời gian $O(\log (\text{num}))$, Không gian $O(1)$.

---

## 20. Arranging Coins
**Đề bài chi tiết**: Bạn có `n` đồng xu và muốn xếp chúng thành các hàng bậc thang. Cụ thể, hàng thứ `i` cần chứa đúng `i` đồng xu. Hàng cuối cùng có thể không đầy. Hãy trả về số hàng tối đa được xếp đầy đủ. Yêu cầu có thể xử lý trong không gian $O(1)$ và không dùng các phương pháp giải phương trình toán học thuần túy.
**Phân tích thuật toán**: Tổng số xu để lấp đầy `k` hàng là $k \times (k + 1) / 2$. Ta có thể áp dụng Binary Search trên khoảng từ $1$ đến $n$. Nếu tổng số xu tại `mid` hàng nhỏ hơn hoặc bằng `n`, ta tìm mốc xa hơn ở nửa phải. Tránh integer overflow bằng cách sử dụng kiểu `long`.
**Mã nguồn Java**:
```java
class Solution {
    public int arrangeCoins(int n) {
        long left = 1, right = n;
        while (left <= right) {
            long mid = left + (right - left) / 2;
            long currentCoins = mid * (mid + 1) / 2;
            
            if (currentCoins == n) return (int) mid;
            else if (currentCoins < n) left = mid + 1;
            else right = mid - 1;
        }
        return (int) right; // Trả về right vì khi loop kết thúc right sẽ chỉ vào hàng max lấp đầy
    }
}
```
**Độ phức tạp**: Thời gian $O(\log N)$, Không gian $O(1)$.

---

## 21. Split Array Largest Sum
**Đề bài chi tiết**: Cho mảng số nguyên không âm `nums` và một số nguyên `k`. Bạn cần chia mảng thành `k` mảng con liên tiếp không rỗng sao cho tổng lớn nhất trong các mảng con là nhỏ nhất có thể. Trả về giá trị tổng lớn nhất thu nhỏ này.
**Phân tích thuật toán**: Không gian tìm kiếm là khoảng từ phần tử lớn nhất trong mảng (khi mỗi phần tử là 1 mảng con, `k = nums.length`) đến tổng tất cả các phần tử (khi `k = 1`). Với mỗi `mid`, ta đếm số mảng con tối thiểu cần thiết để không mảng con nào có tổng vượt quá `mid`. Nếu số mảng con này $\le k$, ta có thể giảm `mid`. Nếu lớn hơn, ta phải tăng `mid`.
**Mã nguồn Java**:
```java
class Solution {
    public int splitArray(int[] nums, int k) {
        int left = 0, right = 0;
        for (int num : nums) {
            left = Math.max(left, num);
            right += num;
        }
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (canSplit(nums, k, mid)) right = mid;
            else left = mid + 1;
        }
        return left;
    }
    
    private boolean canSplit(int[] nums, int k, int maxSum) {
        int count = 1;
        int currentSum = 0;
        for (int num : nums) {
            if (currentSum + num > maxSum) {
                count++;
                currentSum = num;
                if (count > k) return false;
            } else {
                currentSum += num;
            }
        }
        return true;
    }
}
```
**Độ phức tạp**: Thời gian $O(N \log (\sum nums - \max nums))$, Không gian $O(1)$.

---

## 22. Capacity To Ship Packages Within D Days
**Đề bài chi tiết**: Một băng chuyền cần vận chuyển các gói hàng `weights` trong `days` ngày theo đúng thứ tự. Trọng tải tối đa của tàu trong một ngày là bao nhiêu để vận chuyển hết số hàng đúng hạn?
**Phân tích thuật toán**: Tương tự bài 21, sử dụng Binary Search trên kết quả. Trọng tải tối thiểu là `max(weights)` và tối đa là `sum(weights)`. Tại mỗi bước chia đôi `mid`, đếm số ngày cần để chở với khả năng `mid`. Nếu số ngày $\le days$, ta thử tìm khả năng nhỏ hơn.
**Mã nguồn Java**:
```java
class Solution {
    public int shipWithinDays(int[] weights, int days) {
        int left = 0, right = 0;
        for (int w : weights) {
            left = Math.max(left, w);
            right += w;
        }
        while (left < right) {
            int mid = left + (right - left) / 2;
            int neededDays = 1;
            int currentLoad = 0;
            for (int w : weights) {
                if (currentLoad + w > mid) {
                    neededDays++;
                    currentLoad = w;
                } else {
                    currentLoad += w;
                }
            }
            if (neededDays <= days) right = mid;
            else left = mid + 1;
        }
        return left;
    }
}
```
**Độ phức tạp**: Thời gian $O(N \log (\sum weights - \max weights))$, Không gian $O(1)$.

---

## 23. Search in a Sorted Array of Unknown Size
**Đề bài chi tiết**: Cho một mảng nguyên tăng dần nhưng bạn không biết kích thước của nó. Bạn chỉ có thể truy xuất thông qua API `ArrayReader.get(k)`. Nếu `k` ngoài phạm vi mảng, nó trả về `2^{31} - 1`. Hãy tìm chỉ số của `target` trong thời gian $O(\log n)$.
**Phân tích thuật toán**: Khởi tạo hai con trỏ `left = 0` và `right = 1`. Miễn là `reader.get(right) < target`, ta nhảy `left` tới `right` và nhân đôi `right` ($right = right \times 2$). Sau đó áp dụng Binary Search truyền thống trên khoảng `[left, right]`.
**Mã nguồn Java**:
```java
/**
 * // This is ArrayReader's API interface.
 * // You should not implement it, or speculate about its implementation
 * interface ArrayReader {
 *     public int get(int index) {}
 * }
 */
class Solution {
    public int search(ArrayReader reader, int target) {
        int left = 0, right = 1;
        while (reader.get(right) < target) {
            left = right;
            right <<= 1; // Nhân đôi right
        }
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int val = reader.get(mid);
            if (val == target) return mid;
            else if (val > target) right = mid - 1;
            else left = mid + 1;
        }
        return -1;
    }
}
```
**Độ phức tạp**: Thời gian $O(\log (\text{index of target}))$, Không gian $O(1)$.

---

## 24. Koko Eating Bananas
**Đề bài chi tiết**: Khỉ Koko muốn ăn hết các nải chuối trong `piles` (số quả mỗi nải) trong thời gian `h` giờ. Mỗi giờ nó có thể ăn tối đa `k` quả từ một nải (nếu nải có ít hơn `k` quả, nó ăn hết nải đó và nghỉ phần còn lại của giờ). Tìm `k` nhỏ nhất sao cho Koko ăn xong trước khi bảo vệ đến.
**Phân tích thuật toán**: Vẫn là Binary Search trên không gian giá trị đáp án. `k` nằm trong đoạn `[1, max(piles)]`. Tính tổng số giờ dựa trên `mid`. Sử dụng phép chia làm tròn lên để tính số giờ cho mỗi nải.
**Mã nguồn Java**:
```java
class Solution {
    public int minEatingSpeed(int[] piles, int h) {
        int left = 1, right = 1;
        for (int pile : piles) {
            right = Math.max(right, pile);
        }
        while (left < right) {
            int mid = left + (right - left) / 2;
            long hours = 0;
            for (int pile : piles) {
                hours += Math.ceil((double) pile / mid);
            }
            if (hours <= h) right = mid;
            else left = mid + 1;
        }
        return left;
    }
}
```
**Độ phức tạp**: Thời gian $O(N \log (\max(piles)))$, Không gian $O(1)$.

---

## 25. Find Smallest Letter Greater Than Target
**Đề bài chi tiết**: Cho một danh sách các ký tự đã sắp xếp tăng dần `letters` (ít nhất 2 ký tự khác biệt) và một ký tự `target`. Trả về ký tự nhỏ nhất trong danh sách mà lớn hơn `target` theo thứ tự từ vựng. Mảng có tính chất vòng lặp (nếu target lớn hơn tất cả, trả về ký tự đầu tiên).
**Phân tích thuật toán**: Binary Search tìm phần tử đầu tiên lớn hơn `target`. Nếu vòng lặp kết thúc mà `left == letters.length`, ta dùng modulo `% letters.length` để lấy phần tử đầu tiên.
**Mã nguồn Java**:
```java
class Solution {
    public char nextGreatestLetter(char[] letters, char target) {
        int left = 0, right = letters.length;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (letters[mid] <= target) left = mid + 1;
            else right = mid;
        }
        return letters[left % letters.length];
    }
}
```
**Độ phức tạp**: Thời gian $O(\log N)$, Không gian $O(1)$.

---

## 26. Kth Missing Positive Number
**Đề bài chi tiết**: Cho mảng số nguyên dương `arr` sắp xếp tăng dần ngặt và một số nguyên `k`. Tìm số nguyên dương thứ `k` bị thiếu trong dãy.
**Phân tích thuật toán**: Tại một vị trí `i`, số lượng số bị thiếu là `arr[i] - i - 1`. Ta dùng Binary Search tìm vị trí lớn nhất mà số phần tử thiếu vẫn nhỏ hơn `k`. Vị trí đó là `right` hoặc `left - 1`. Sau đó số thiếu được tính là `k - missing_count + arr[right]` = `left + k`.
**Mã nguồn Java**:
```java
class Solution {
    public int findKthPositive(int[] arr, int k) {
        int left = 0, right = arr.length;
        while (left < right) {
            int mid = left + (right - left) / 2;
            int missing = arr[mid] - mid - 1;
            if (missing < k) left = mid + 1;
            else right = mid;
        }
        return left + k;
    }
}
```
**Độ phức tạp**: Thời gian $O(\log N)$, Không gian $O(1)$.

---

## 27. Magnetic Force Between Two Balls
**Đề bài chi tiết**: Trong một rổ có `m` quả bóng từ tính. Cho mảng `position` ghi lại các vị trí giỏ trên một trục tọa độ 1D. Bạn cần đặt `m` quả bóng vào các giỏ sao cho "lực từ nhỏ nhất" (tức là khoảng cách nhỏ nhất giữa hai quả bóng bất kỳ) là lớn nhất có thể.
**Phân tích thuật toán**: Binary Search trên giá trị khoảng cách. Khoảng cách nhỏ nhất là $1$, lớn nhất là `position[n-1] - position[0]`. Với một khoảng cách `mid`, dùng Greedy để đặt bóng: quả đầu luôn ở `position[0]`, quả tiếp theo phải cách quả trước $\ge mid$. Nếu đặt được $\ge m$ quả, `mid` có thể khả thi, ta thử tăng nó lên.
**Mã nguồn Java**:
```java
import java.util.Arrays;

class Solution {
    public int maxDistance(int[] position, int m) {
        Arrays.sort(position);
        int n = position.length;
        int left = 1, right = position[n - 1] - position[0];
        int ans = -1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (canPlaceBalls(position, m, mid)) {
                ans = mid;
                left = mid + 1; // Thử mở rộng khoảng cách
            } else {
                right = mid - 1; // Thu hẹp khoảng cách
            }
        }
        return ans;
    }
    
    private boolean canPlaceBalls(int[] position, int m, int minForce) {
        int count = 1;
        int lastPos = position[0];
        for (int i = 1; i < position.length; i++) {
            if (position[i] - lastPos >= minForce) {
                count++;
                lastPos = position[i];
                if (count == m) return true;
            }
        }
        return false;
    }
}
```
**Độ phức tạp**: Thời gian $O(N \log N + N \log(\max - \min))$, Không gian $O(1)$.

---

## 28. Missing Element in Sorted Array
**Đề bài chi tiết**: Cho mảng số nguyên được sắp xếp tăng dần ngặt `nums` và một số `k`. Hãy tìm số thứ `k` bị thiếu tính từ phần tử bên trái ngoài cùng.
**Phân tích thuật toán**: Khá giống bài 26, số lượng phần tử thiếu tính từ đầu mảng đến vị trí `mid` là `missing = nums[mid] - nums[0] - mid`. Dùng Binary Search để tìm đoạn chứa số bị thiếu. Khi kết thúc, phần tử cần tìm là `nums[left - 1] + (k - missing_at_left_minus_1)`.
**Mã nguồn Java**:
```java
class Solution {
    public int missingElement(int[] nums, int k) {
        int left = 0, right = nums.length;
        while (left < right) {
            int mid = left + (right - left) / 2;
            int missing = nums[mid] - nums[0] - mid;
            
            if (missing < k) left = mid + 1;
            else right = mid;
        }
        // Tính từ vị trí left - 1
        int leftIndex = left - 1;
        int missingBefore = nums[leftIndex] - nums[0] - leftIndex;
        return nums[leftIndex] + (k - missingBefore);
    }
}
```
**Độ phức tạp**: Thời gian $O(\log N)$, Không gian $O(1)$.

---

## 29. Search a 2D Matrix
**Đề bài chi tiết**: Cho ma trận số nguyên kích thước $m \times n$. Hàng được sắp xếp tăng dần, số đầu tiên của hàng tiếp theo lớn hơn số cuối cùng của hàng trước. Tìm `target` trong ma trận trong $O(\log(m \times n))$.
**Phân tích thuật toán**: Trải phẳng (flatten) ma trận thành mảng 1D, lấy `mid` trong khoảng `[0, m * n - 1]`, chuyển ngược tọa độ về dạng `(mid / n, mid % n)`.
**Mã nguồn Java**:
```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix.length == 0 || matrix[0].length == 0) return false;
        int m = matrix.length, n = matrix[0].length;
        int left = 0, right = m * n - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int midValue = matrix[mid / n][mid % n];
            
            if (midValue == target) return true;
            else if (midValue < target) left = mid + 1;
            else right = mid - 1;
        }
        return false;
    }
}
```
**Độ phức tạp**: Thời gian $O(\log(m \times n))$, Không gian $O(1)$.

---

## 30. Search a 2D Matrix II
**Đề bài chi tiết**: Cho ma trận $m \times n$, các hàng tăng dần trái sang phải, các cột tăng dần trên xuống dưới. Tìm `target`. Yêu cầu thuật toán nhanh và tối ưu không gian.
**Phân tích thuật toán**: Ta có thể duyệt ma trận bắt đầu từ góc trên bên phải (hoặc góc dưới bên trái). Nếu giá trị tại góc nhỏ hơn `target`, ta loại bỏ toàn bộ hàng hiện tại (chuyển xuống dưới). Nếu giá trị đó lớn hơn `target`, ta loại bỏ toàn bộ cột (chuyển sang trái).
**Mã nguồn Java**:
```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return false;
        int row = 0, col = matrix[0].length - 1;
        
        while (row < matrix.length && col >= 0) {
            if (matrix[row][col] == target) {
                return true;
            } else if (matrix[row][col] > target) {
                col--; // Loại bỏ cột
            } else {
                row++; // Loại bỏ hàng
            }
        }
        return false;
    }
}
```
**Độ phức tạp**: Thời gian $O(m + n)$, Không gian $O(1)$. Thuật toán này mô phỏng việc loại trừ dần không gian (thu hẹp) mượn ý tưởng tương tự như two-pointers thay vì Binary Search thuần.
