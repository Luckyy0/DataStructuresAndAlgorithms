# 03 - Partitioning and Selection Problems (Bài tập Phân Hoạch và Lựa Chọn)

Tài liệu này bao gồm 30 bài toán phổ biến về Phân Hoạch (Partitioning) và Lựa Chọn (Selection). Cả 30 bài đều được phân tích chi tiết kèm mã nguồn Java và phân tích độ phức tạp.

## 1. Kth Largest Element in an Array (LeetCode 215)
**Đề bài chi tiết**: Cho một mảng số nguyên `nums` chưa được sắp xếp và một số nguyên `k`. Trả về phần tử lớn thứ `k` trong mảng. Yêu cầu độ phức tạp thời gian là $O(N)$.
**Phân tích thuật toán**: Sử dụng Quick Select với Randomized Pivot hoặc Min-Heap kích thước K. Để đạt được $O(N)$ trung bình in-place, ta chọn Quick Select. Phần tử lớn thứ `k` tương đương với phần tử nằm ở vị trí `nums.length - k` nếu mảng được sắp xếp tăng dần.
**Mã nguồn Java**:
```java
import java.util.Random;

public class Solution {
    public int findKthLargest(int[] nums, int k) {
        int targetIndex = nums.length - k;
        int left = 0, right = nums.length - 1;
        Random rand = new Random();
        
        while (left <= right) {
            int pivotIndex = left + rand.nextInt(right - left + 1);
            int finalPivotIndex = partition(nums, left, right, pivotIndex);
            
            if (finalPivotIndex == targetIndex) {
                return nums[finalPivotIndex];
            } else if (finalPivotIndex < targetIndex) {
                left = finalPivotIndex + 1;
            } else {
                right = finalPivotIndex - 1;
            }
        }
        return -1;
    }
    
    private int partition(int[] nums, int left, int right, int pivotIndex) {
        int pivotValue = nums[pivotIndex];
        swap(nums, pivotIndex, right);
        int storeIndex = left;
        
        for (int i = left; i < right; i++) {
            if (nums[i] < pivotValue) {
                swap(nums, storeIndex, i);
                storeIndex++;
            }
        }
        swap(nums, storeIndex, right);
        return storeIndex;
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```
**Độ phức tạp**:
- Time: Trung bình $O(N)$, Worst-case $O(N^2)$ (đã giảm thiểu bằng Randomized pivot).
- Space: $O(1)$ (Iterative Quick Select).

## 2. Top K Frequent Elements (LeetCode 347)
**Đề bài chi tiết**: Cho mảng số nguyên `nums` và một số `k`, trả về `k` phần tử xuất hiện nhiều nhất. Bạn có thể trả về đáp án theo bất kỳ thứ tự nào. Thời gian yêu cầu tốt hơn $O(N \log N)$.
**Phân tích thuật toán**: 
1. Đếm tần suất các số bằng HashMap.
2. Dùng Bucket Sort (mảng các List, chỉ số là tần suất) do tần suất tối đa là N.
3. Hoặc dùng Quick Select trên mảng các Unique Elements dựa trên tần suất để có Time $O(N)$ trung bình.
**Mã nguồn Java (Quick Select)**:
```java
import java.util.*;

public class Solution {
    Map<Integer, Integer> count = new HashMap<>();
    
    public int[] topKFrequent(int[] nums, int k) {
        for (int num : nums) {
            count.put(num, count.getOrDefault(num, 0) + 1);
        }
        
        int n = count.size();
        int[] unique = new int[n];
        int i = 0;
        for (int num : count.keySet()) {
            unique[i++] = num;
        }
        
        quickSelect(unique, 0, n - 1, n - k);
        return Arrays.copyOfRange(unique, n - k, n);
    }
    
    private void quickSelect(int[] arr, int left, int right, int kSmallest) {
        if (left >= right) return;
        int pivotIndex = left + new Random().nextInt(right - left + 1);
        pivotIndex = partition(arr, left, right, pivotIndex);
        
        if (kSmallest == pivotIndex) return;
        else if (kSmallest < pivotIndex) quickSelect(arr, left, pivotIndex - 1, kSmallest);
        else quickSelect(arr, pivotIndex + 1, right, kSmallest);
    }
    
    private int partition(int[] arr, int left, int right, int pivotIndex) {
        int pivotFreq = count.get(arr[pivotIndex]);
        swap(arr, pivotIndex, right);
        int storeIndex = left;
        
        for (int i = left; i < right; i++) {
            if (count.get(arr[i]) < pivotFreq) {
                swap(arr, storeIndex, i);
                storeIndex++;
            }
        }
        swap(arr, storeIndex, right);
        return storeIndex;
    }
    
    private void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```
**Độ phức tạp**:
- Time: Trung bình $O(N)$, Worst-case $O(N^2)$.
- Space: $O(N)$ lưu trữ Hash Map.

## 3. Sort Colors (Dutch National Flag) (LeetCode 75)
**Đề bài chi tiết**: Cho mảng `nums` chứa n đối tượng với các màu đỏ, trắng, xanh lam, sắp xếp chúng in-place sao cho các màu giống nhau ở cạnh nhau theo thứ tự đỏ, trắng, xanh lam. (Các màu đại diện bởi 0, 1, 2). Không sử dụng thư viện sort.
**Phân tích thuật toán**: Bài toán 3-way partitioning. Duy trì 3 con trỏ: `low` chỉ nơi chèn 0 tiếp theo, `high` chỉ nơi chèn 2 tiếp theo, `mid` để duyệt mảng. Gặp 0 swap với `low`, gặp 2 swap với `high`.
**Mã nguồn Java**:
```java
public class Solution {
    public void sortColors(int[] nums) {
        int low = 0;
        int mid = 0;
        int high = nums.length - 1;
        
        while (mid <= high) {
            if (nums[mid] == 0) {
                swap(nums, low, mid);
                low++;
                mid++;
            } else if (nums[mid] == 1) {
                mid++;
            } else {
                swap(nums, mid, high);
                high--;
            }
        }
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```
**Độ phức tạp**:
- Time: $O(N)$ (duyệt một lần).
- Space: $O(1)$ (in-place).

## 4. Wiggle Sort II (LeetCode 324)
**Đề bài chi tiết**: Cho mảng nguyên `nums`, sắp xếp nó sao cho `nums[0] < nums[1] > nums[2] < nums[3]...`. Yêu cầu Time $O(N)$, Space $O(1)$ (khó).
**Phân tích thuật toán**: 
1. Dùng Quick Select tìm phần tử Median. 
2. Áp dụng Virtual Indexing (Ánh xạ vị trí 3-way partition sang index chẵn/lẻ) để xếp các số lớn hơn median vào index lẻ, nhỏ hơn median vào index chẵn.
**Mã nguồn Java**:
```java
import java.util.Arrays;

public class Solution {
    public void wiggleSort(int[] nums) {
        int n = nums.length;
        int median = findKthLargest(nums, (n + 1) / 2);
        
        int left = 0, i = 0, right = n - 1;
        // Virtual Indexing & 3-way partition
        while (i <= right) {
            if (nums[newIndex(i, n)] > median) {
                swap(nums, newIndex(left++, n), newIndex(i++, n));
            } else if (nums[newIndex(i, n)] < median) {
                swap(nums, newIndex(right--, n), newIndex(i, n));
            } else {
                i++;
            }
        }
    }
    
    private int newIndex(int index, int n) {
        return (1 + 2 * index) % (n | 1);
    }
    
    // Quick Select Code Omitted for brevity (same as Problem 1)
    private int findKthLargest(int[] nums, int k) {
        Arrays.sort(nums); // Trong thực tế dùng QuickSelect để có O(N)
        return nums[nums.length - k]; 
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```
**Độ phức tạp**:
- Time: $O(N)$ nếu dùng Quick Select.
- Space: $O(1)$ in-place (Virtual Indexing trick).

## 5. K Closest Points to Origin (LeetCode 973)
**Đề bài chi tiết**: Cho danh sách `points` trên mặt phẳng tọa độ 2D và số nguyên `k`. Tìm `k` điểm gần gốc tọa độ (0, 0) nhất.
**Phân tích thuật toán**: Tính khoảng cách Euclid (bình phương). Sau đó bài toán trở thành tìm K-th Smallest element bằng Quick Select trên danh sách đối tượng / mảng.
**Mã nguồn Java**:
```java
import java.util.Random;

public class Solution {
    public int[][] kClosest(int[][] points, int k) {
        int left = 0, right = points.length - 1;
        Random rand = new Random();
        while (left <= right) {
            int pivotIndex = left + rand.nextInt(right - left + 1);
            int p = partition(points, left, right, pivotIndex);
            
            if (p == k - 1) {
                break;
            } else if (p < k - 1) {
                left = p + 1;
            } else {
                right = p - 1;
            }
        }
        
        int[][] res = new int[k][2];
        for (int i = 0; i < k; i++) {
            res[i] = points[i];
        }
        return res;
    }
    
    private int partition(int[][] points, int left, int right, int pivotIndex) {
        int pivotDist = getDist(points[pivotIndex]);
        swap(points, pivotIndex, right);
        int storeIndex = left;
        
        for (int i = left; i < right; i++) {
            if (getDist(points[i]) < pivotDist) {
                swap(points, storeIndex, i);
                storeIndex++;
            }
        }
        swap(points, storeIndex, right);
        return storeIndex;
    }
    
    private int getDist(int[] point) {
        return point[0] * point[0] + point[1] * point[1];
    }
    
    private void swap(int[][] points, int i, int j) {
        int[] temp = points[i];
        points[i] = points[j];
        points[j] = temp;
    }
}
```
**Độ phức tạp**:
- Time: Trung bình $O(N)$, Worst-case $O(N^2)$.
- Space: $O(1)$ (in-place modification of input).

## 6. Find K-th Smallest Pair Distance (LeetCode 719)
**Đề bài chi tiết**: Cho mảng `nums` nguyên, cặp `(nums[i], nums[j])` có khoảng cách `|nums[i] - nums[j]|`. Trả về khoảng cách nhỏ thứ k trong số mọi khoảng cách có thể.
**Phân tích thuật toán**: Bài toán kết hợp Binary Search on Answer và Sliding Window (hoặc Two Pointers) không hẳn là Partitioning truyền thống nhưng thuộc bài toán Selection.
**Mã nguồn Java**:
```java
import java.util.Arrays;

public class Solution {
    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums);
        int left = 0;
        int right = nums[nums.length - 1] - nums[0];
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            int count = countPairs(nums, mid);
            
            if (count >= k) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        
        return left;
    }
    
    private int countPairs(int[] nums, int mid) {
        int count = 0, left = 0;
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
**Độ phức tạp**:
- Time: $O(N \log N + N \log W)$, với W là Max Diff.
- Space: $O(1)$ / $O(\log N)$ tùy thuộc Sort.

## 7. The K Weakest Rows in a Matrix (LeetCode 1337)
**Đề bài chi tiết**: Ma trận `mat` chỉ chứa 0 và 1, 1 đại diện cho lính, 0 dân thường (1 luôn đứng trước 0). Trả về chỉ số k hàng yếu nhất (ít lính hơn hoặc cùng lính thì chỉ số nhỏ hơn).
**Phân tích thuật toán**: Binary search đếm số lượng 1 mỗi hàng. Dùng Quick Select hoặc Heap để tìm k hàng yếu nhất dựa trên cặp giá trị (Soldiers, RowIndex).
**Mã nguồn Java**:
```java
import java.util.PriorityQueue;

public class Solution {
    public int[] kWeakestRows(int[][] mat, int k) {
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) -> {
            if (a[0] == b[0]) return b[1] - a[1];
            return b[0] - a[0];
        });
        
        for (int i = 0; i < mat.length; i++) {
            int soldiers = binarySearch(mat[i]);
            maxHeap.offer(new int[]{soldiers, i});
            if (maxHeap.size() > k) {
                maxHeap.poll();
            }
        }
        
        int[] res = new int[k];
        for (int i = k - 1; i >= 0; i--) {
            res[i] = maxHeap.poll()[1];
        }
        return res;
    }
    
    private int binarySearch(int[] row) {
        int left = 0, right = row.length;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (row[mid] == 1) left = mid + 1;
            else right = mid;
        }
        return left;
    }
}
```
**Độ phức tạp**:
- Time: $O(M \log N + M \log K)$.
- Space: $O(K)$ cho Heap.

## 8. Pancake Sorting (LeetCode 969)
**Đề bài chi tiết**: Cho mảng nguyên `arr`, sắp xếp mảng bằng cách lật (`flip(k)` lật k phần tử đầu tiên của mảng). Trả về dãy các `k` để sắp xếp mảng.
**Phân tích thuật toán**: Giống Selection Sort. Tìm phần tử lớn nhất chưa vào đúng vị trí, lật nó lên đầu, sau đó lật nó về vị trí đúng ở cuối. Lặp lại cho tất cả các phần tử.
**Mã nguồn Java**:
```java
import java.util.*;

public class Solution {
    public List<Integer> pancakeSort(int[] arr) {
        List<Integer> res = new ArrayList<>();
        for (int n = arr.length; n > 0; n--) {
            int maxIdx = findMaxIdx(arr, n);
            if (maxIdx == n - 1) continue;
            
            if (maxIdx != 0) {
                res.add(maxIdx + 1);
                flip(arr, maxIdx + 1);
            }
            res.add(n);
            flip(arr, n);
        }
        return res;
    }
    
    private int findMaxIdx(int[] arr, int k) {
        int maxIdx = 0;
        for (int i = 1; i < k; i++) {
            if (arr[i] > arr[maxIdx]) maxIdx = i;
        }
        return maxIdx;
    }
    
    private void flip(int[] arr, int k) {
        int i = 0, j = k - 1;
        while (i < j) {
            int temp = arr[i];
            arr[i++] = arr[j];
            arr[j--] = temp;
        }
    }
}
```
**Độ phức tạp**:
- Time: $O(N^2)$ (tìm Max và Flip mỗi bước).
- Space: $O(N)$ lưu List đáp án.

## 9. Median of Medians implementation (Custom Problem)
**Đề bài chi tiết**: Cài đặt thuật toán Median of Medians để tìm K-th Smallest element với độ phức tạp $O(N)$ trong trường hợp xấu nhất (Deterministic Time).
**Phân tích thuật toán**: Chia mảng thành nhóm 5 phần tử. Tìm median mỗi nhóm (bằng Insertion Sort). Đệ quy tìm median của tập hợp medians (gọi là m). Dùng m làm pivot cho Quick Select.
**Mã nguồn Java**:
```java
import java.util.Arrays;

public class Solution {
    public int findKthSmallest(int[] arr, int k) {
        return select(arr, 0, arr.length - 1, k - 1);
    }
    
    private int select(int[] arr, int left, int right, int kIndex) {
        if (left == right) return arr[left];
        
        int pivot = medianOfMedians(arr, left, right);
        int pivotIndex = getIndex(arr, left, right, pivot);
        pivotIndex = partition(arr, left, right, pivotIndex);
        
        if (kIndex == pivotIndex) return arr[kIndex];
        else if (kIndex < pivotIndex) return select(arr, left, pivotIndex - 1, kIndex);
        else return select(arr, pivotIndex + 1, right, kIndex);
    }
    
    private int medianOfMedians(int[] arr, int left, int right) {
        int n = right - left + 1;
        if (n < 5) {
            Arrays.sort(arr, left, right + 1);
            return arr[left + n / 2];
        }
        
        int[] medians = new int[(n + 4) / 5];
        for (int i = 0; i < medians.length; i++) {
            int subLeft = left + i * 5;
            int subRight = Math.min(left + i * 5 + 4, right);
            Arrays.sort(arr, subLeft, subRight + 1);
            medians[i] = arr[subLeft + (subRight - subLeft) / 2];
        }
        
        return select(medians, 0, medians.length - 1, medians.length / 2);
    }
    
    private int partition(int[] arr, int left, int right, int pivotIndex) {
        int pivotValue = arr[pivotIndex];
        swap(arr, pivotIndex, right);
        int store = left;
        for (int i = left; i < right; i++) {
            if (arr[i] < pivotValue) {
                swap(arr, store++, i);
            }
        }
        swap(arr, store, right);
        return store;
    }
    
    private int getIndex(int[] arr, int left, int right, int val) {
        for (int i = left; i <= right; i++) if (arr[i] == val) return i;
        return -1;
    }
    
    private void swap(int[] arr, int i, int j) {
        int temp = arr[i]; arr[i] = arr[j]; arr[j] = temp;
    }
}
```
**Độ phức tạp**:
- Time: $O(N)$ Deterministic.
- Space: $O(N)$ do tạo mảng `medians`.

## 10. Kth Largest Element in a Stream (LeetCode 703)
**Đề bài chi tiết**: Thiết kế một class tìm phần tử lớn thứ k trong một stream. Chú ý ta cần phần tử thứ k trong thứ tự đã sắp xếp, không phải giá trị phân biệt lớn thứ k.
**Phân tích thuật toán**: Lưu 1 Min-Heap có kích thước $K$. Với mỗi phần tử mới `val`, thêm vào Heap. Nếu kích thước Heap > K, ta pop Min ra. Trả về đỉnh Heap.
**Mã nguồn Java**:
```java
import java.util.PriorityQueue;

class KthLargest {
    private PriorityQueue<Integer> minHeap;
    private int k;

    public KthLargest(int k, int[] nums) {
        this.k = k;
        minHeap = new PriorityQueue<>(k);
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
**Độ phức tạp**:
- Time: $O(N \log K)$ khi khởi tạo, $O(\log K)$ mỗi thao tác `add`.
- Space: $O(K)$ để lưu Min-Heap.

## 11. Partition List (LeetCode 86)
**Đề bài chi tiết**: Cho `head` của một danh sách liên kết (Linked List) và một giá trị `x`, phân hoạch danh sách sao cho tất cả các node có giá trị nhỏ hơn `x` nằm trước các node có giá trị lớn hơn hoặc bằng `x`. Bạn nên bảo toàn thứ tự tương đối ban đầu của các node trong cả hai phần.
**Phân tích thuật toán**: Tạo hai danh sách liên kết ảo (dummy nodes): một để lưu các node nhỏ hơn `x` (`before`) và một để lưu các node lớn hơn hoặc bằng `x` (`after`). Duyệt qua danh sách gốc và thêm từng node vào danh sách tương ứng. Cuối cùng, nối đuôi của `before` với đầu của `after` và kết thúc danh sách `after` bằng `null`.
**Mã nguồn Java**:
```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int val) { this.val = val; }
}

public class Solution {
    public ListNode partition(ListNode head, int x) {
        ListNode beforeHead = new ListNode(0);
        ListNode before = beforeHead;
        ListNode afterHead = new ListNode(0);
        ListNode after = afterHead;
        
        while (head != null) {
            if (head.val < x) {
                before.next = head;
                before = before.next;
            } else {
                after.next = head;
                after = after.next;
            }
            head = head.next;
        }
        
        after.next = null;
        before.next = afterHead.next;
        
        return beforeHead.next;
    }
}
```
**Độ phức tạp**:
- Time: $O(N)$ do duyệt qua mảng một lần.
- Space: $O(1)$ (chỉ sử dụng một vài con trỏ, in-place manipulation).

## 12. Partition Equal Subset Sum (LeetCode 416)
**Đề bài chi tiết**: Cho một mảng số nguyên dương `nums`. Xác định xem mảng có thể được chia thành hai tập con sao cho tổng các phần tử trong cả hai tập con bằng nhau không.
**Phân tích thuật toán**: Bài toán quy về bài toán cái túi (Knapsack Problem). Nếu tổng các phần tử của mảng là một số lẻ, ta không thể chia mảng thành hai phần bằng nhau. Nếu là số chẵn, mục tiêu là tìm xem có tập con nào có tổng bằng `sum / 2` hay không. Sử dụng Quy hoạch động (Dynamic Programming) mảng 1 chiều `dp[j]` để lưu trạng thái xem có thể tạo ra tổng `j` hay không.
**Mã nguồn Java**:
```java
public class Solution {
    public boolean canPartition(int[] nums) {
        int sum = 0;
        for (int num : nums) {
            sum += num;
        }
        
        if (sum % 2 != 0) return false;
        
        int target = sum / 2;
        boolean[] dp = new boolean[target + 1];
        dp[0] = true;
        
        for (int num : nums) {
            for (int j = target; j >= num; j--) {
                dp[j] = dp[j] || dp[j - num];
            }
        }
        
        return dp[target];
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times \text{target})$ với target là nửa tổng mảng.
- Space: $O(\text{target})$ cho mảng `dp`.

## 13. Array Partition I (LeetCode 561)
**Đề bài chi tiết**: Cho mảng số nguyên `nums` có `2n` phần tử, hãy nhóm các phần tử thành `n` cặp `(a1, b1), (a2, b2), ..., (an, bn)` sao cho tổng của `min(ai, bi)` đối với tất cả `i` là lớn nhất. Trả về tổng lớn nhất có thể.
**Phân tích thuật toán**: Để làm cho giá trị `min(ai, bi)` càng lớn càng tốt, khoảng cách giữa `a` và `b` trong mỗi cặp phải càng nhỏ càng tốt. Bằng cách sắp xếp mảng và ghép các phần tử kề nhau, ta có thể tối đa hóa tổng các giá trị nhỏ nhất của các cặp.
**Mã nguồn Java**:
```java
import java.util.Arrays;

public class Solution {
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
**Độ phức tạp**:
- Time: $O(N \log N)$ cho việc sắp xếp mảng.
- Space: $O(1)$ (không tính không gian stack của thuật toán sắp xếp).

## 14. Partition Labels (LeetCode 763)
**Đề bài chi tiết**: Cho một chuỗi `s` chứa các chữ cái tiếng Anh in thường. Phân hoạch chuỗi thành nhiều phần nhất có thể sao cho mỗi chữ cái chỉ xuất hiện trong tối đa một phần. Trả về một mảng chứa kích thước của các phần này.
**Phân tích thuật toán**: Thuật toán Greedy (Tham lam). Đầu tiên, duyệt qua chuỗi để lưu vị trí xuất hiện cuối cùng của mỗi ký tự. Tiếp theo, duyệt lại chuỗi và duy trì một biến `end` cập nhật vị trí xuất hiện cuối cùng của các ký tự đang duyệt. Khi chỉ số vòng lặp `i == end`, ta có thể tách một phần.
**Mã nguồn Java**:
```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public List<Integer> partitionLabels(String s) {
        int[] last = new int[26];
        for (int i = 0; i < s.length(); i++) {
            last[s.charAt(i) - 'a'] = i;
        }
        
        List<Integer> res = new ArrayList<>();
        int start = 0, end = 0;
        for (int i = 0; i < s.length(); i++) {
            end = Math.max(end, last[s.charAt(i) - 'a']);
            if (i == end) {
                res.add(end - start + 1);
                start = i + 1;
            }
        }
        return res;
    }
}
```
**Độ phức tạp**:
- Time: $O(N)$ vì duyệt chuỗi 2 lần.
- Space: $O(1)$ (mảng `last` có kích thước cố định là 26).

## 15. Minimize Maximum Pair Sum in Array (LeetCode 1877)
**Đề bài chi tiết**: Cho một mảng `nums` gồm các số nguyên dương có số lượng phần tử chẵn `n`. Phân chia mảng thành `n / 2` cặp sao cho tổng lớn nhất của các cặp là nhỏ nhất. Trả về tổng lớn nhất tối thiểu đó.
**Phân tích thuật toán**: Để cực tiểu hóa phần tử lớn nhất trong tổng các cặp, ta nên ghép phần tử lớn nhất hiện tại với phần tử nhỏ nhất hiện tại. Thuật toán tham lam: Sắp xếp mảng tăng dần, sau đó dùng 2 con trỏ ở hai đầu ghép `nums[i] + nums[n - 1 - i]`, và tìm giá trị lớn nhất.
**Mã nguồn Java**:
```java
import java.util.Arrays;

public class Solution {
    public int minPairSum(int[] nums) {
        Arrays.sort(nums);
        int maxPairSum = 0;
        int n = nums.length;
        
        for (int i = 0; i < n / 2; i++) {
            maxPairSum = Math.max(maxPairSum, nums[i] + nums[n - 1 - i]);
        }
        
        return maxPairSum;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \log N)$ do sắp xếp mảng.
- Space: $O(1)$ (chỉ dùng biến đếm và duyệt).

## 16. Global and Local Inversions (LeetCode 775)
**Đề bài chi tiết**: Cho một mảng `nums` có độ dài `n` là một hoán vị của các số từ `0` đến `n - 1`. Một Local Inversion là một chỉ số `i` sao cho `nums[i] > nums[i+1]`. Một Global Inversion là một cặp `(i, j)` sao cho `0 <= i < j < n` và `nums[i] > nums[j]`. Trả về `true` nếu số lượng global inversions bằng local inversions.
**Phân tích thuật toán**: Mọi Local Inversion đều là Global Inversion. Để 2 số lượng này bằng nhau, không được phép có Global Inversion nào không phải là Local Inversion. Nghĩa là không được có `nums[i] > nums[j]` với `j - i > 1`. Ta chỉ cần duy trì biến `max` ghi lại giá trị lớn nhất từ `0` đến `i - 2` và kiểm tra nếu `max > nums[i]`. Hoặc với tính chất hoán vị, ta chỉ cần kiểm tra xem có phần tử nào bị dịch chuyển quá 1 đơn vị không: `|nums[i] - i| > 1`.
**Mã nguồn Java**:
```java
public class Solution {
    public boolean isIdealPermutation(int[] nums) {
        // Cách 1: Math trick 
        for (int i = 0; i < nums.length; i++) {
            if (Math.abs(nums[i] - i) > 1) {
                return false;
            }
        }
        return true;
        
        /* 
        // Cách 2: Lưu trữ max prefix
        int max = -1;
        for (int i = 0; i < nums.length - 2; i++) {
            max = Math.max(max, nums[i]);
            if (max > nums[i + 2]) {
                return false;
            }
        }
        return true;
        */
    }
}
```
**Độ phức tạp**:
- Time: $O(N)$ do duyệt một vòng.
- Space: $O(1)$.

## 17. Top K Frequent Words (LeetCode 692)
**Đề bài chi tiết**: Cho một mảng chuỗi `words` và số nguyên `k`. Trả về `k` từ xuất hiện nhiều nhất. Phải sắp xếp các từ theo thứ tự giảm dần của tần suất. Nếu có cùng tần suất, xếp theo thứ tự từ điển.
**Phân tích thuật toán**: Dùng HashMap đếm tần suất. Sau đó đưa các entry vào Min-Heap có kích thước `k` với comparator custom: Nếu tần suất khác nhau thì so sánh tần suất, nếu bằng nhau thì so sánh chuỗi (ngược lại từ điển để từ lớn nhất rơi xuống đáy min-heap). Cuối cùng lấy từ Min-Heap ra và đảo ngược kết quả.
**Mã nguồn Java**:
```java
import java.util.*;

public class Solution {
    public List<String> topKFrequent(String[] words, int k) {
        Map<String, Integer> count = new HashMap<>();
        for (String word : words) {
            count.put(word, count.getOrDefault(word, 0) + 1);
        }
        
        PriorityQueue<String> minHeap = new PriorityQueue<>((w1, w2) -> {
            int freq1 = count.get(w1);
            int freq2 = count.get(w2);
            if (freq1 == freq2) {
                return w2.compareTo(w1); // Lưu ý: Min Heap thì string xếp ngược thứ tự từ điển
            }
            return freq1 - freq2; // Tần suất ít hơn nằm trên đỉnh để bị pop
        });
        
        for (String word : count.keySet()) {
            minHeap.offer(word);
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }
        
        List<String> res = new ArrayList<>();
        while (!minHeap.isEmpty()) {
            res.add(minHeap.poll());
        }
        Collections.reverse(res);
        return res;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \log K)$.
- Space: $O(N)$ cho HashMap và Min-Heap.

## 18. Third Maximum Number (LeetCode 414)
**Đề bài chi tiết**: Cho một mảng số nguyên `nums`, trả về số lớn thứ ba trong mảng. Nếu không tồn tại, trả về số lớn nhất. Lưu ý phải bỏ qua các phần tử trùng lặp. Yêu cầu Time $O(N)$.
**Phân tích thuật toán**: Duy trì 3 biến `max1`, `max2`, `max3` dạng `Integer` object để có thể biểu diễn giá trị `null` ban đầu (vì mảng có thể chứa giá trị `Integer.MIN_VALUE`). Khi duyệt qua mảng, bỏ qua nếu phần tử đã bằng 1 trong 3 biến. Cập nhật lần lượt các biến nếu tìm thấy phần tử lớn hơn.
**Mã nguồn Java**:
```java
public class Solution {
    public int thirdMax(int[] nums) {
        Integer max1 = null;
        Integer max2 = null;
        Integer max3 = null;
        
        for (Integer n : nums) {
            if (n.equals(max1) || n.equals(max2) || n.equals(max3)) {
                continue;
            }
            
            if (max1 == null || n > max1) {
                max3 = max2;
                max2 = max1;
                max1 = n;
            } else if (max2 == null || n > max2) {
                max3 = max2;
                max2 = n;
            } else if (max3 == null || n > max3) {
                max3 = n;
            }
        }
        
        return max3 == null ? max1 : max3;
    }
}
```
**Độ phức tạp**:
- Time: $O(N)$ do duyệt một vòng.
- Space: $O(1)$.

## 19. Majority Element (LeetCode 169)
**Đề bài chi tiết**: Cho mảng `nums` kích thước `n`, trả về phần tử đa số (majority element). Phần tử đa số là phần tử xuất hiện nhiều hơn `⌊n / 2⌋` lần. Có thể giả định mảng luôn có phần tử đa số.
**Phân tích thuật toán**: Dạng bài selection đặc biệt. Có thể dùng HashMap (Space $O(N)$), Sắp xếp và lấy trung vị (Time $O(N \log N)$), Quick Select tìm trung vị (Time $O(N)$, Space $O(1)$) hoặc Boyer-Moore Voting Algorithm để có Time $O(N)$ và Space $O(1)$ in-place và deterministic.
**Mã nguồn Java**:
```java
public class Solution {
    public int majorityElement(int[] nums) {
        // Thuật toán Boyer-Moore Voting
        int count = 0;
        int candidate = 0;
        
        for (int num : nums) {
            if (count == 0) {
                candidate = num;
            }
            count += (num == candidate) ? 1 : -1;
        }
        
        return candidate;
    }
}
```
**Độ phức tạp**:
- Time: $O(N)$ do duyệt một vòng.
- Space: $O(1)$.

## 20. Find Median from Data Stream (LeetCode 295)
**Đề bài chi tiết**: Implement lớp `MedianFinder` hỗ trợ hai thao tác: `addNum(int num)` thêm một số nguyên từ data stream, và `findMedian()` trả về giá trị trung vị của tất cả các phần tử.
**Phân tích thuật toán**: Sử dụng hai Heaps: Một Max-Heap lưu trữ nửa nhỏ của các phần tử và một Min-Heap lưu trữ nửa lớn. Để duy trì tính trung vị, ta luôn duy trì số lượng phần tử của Max-Heap bằng số lượng phần tử Min-Heap (cho tổng số chẵn) hoặc lớn hơn 1 phần tử (cho tổng số lẻ). Mỗi khi thêm số mới vào, chèn vào Max-Heap trước, pop phần tử lớn nhất chuyển sang Min-Heap. Nếu chênh lệch size quá quy định, pop từ Min-Heap trả về Max-Heap.
**Mã nguồn Java**:
```java
import java.util.PriorityQueue;

class MedianFinder {
    private PriorityQueue<Integer> smallHeap; // Max-Heap
    private PriorityQueue<Integer> largeHeap; // Min-Heap

    public MedianFinder() {
        smallHeap = new PriorityQueue<>((a, b) -> b - a);
        largeHeap = new PriorityQueue<>();
    }
    
    public void addNum(int num) {
        smallHeap.offer(num);
        largeHeap.offer(smallHeap.poll()); // Chuyển số lớn nhất bên small sang large
        
        // Cân bằng size: smallHeap luôn chứa số lượng >= largeHeap
        if (smallHeap.size() < largeHeap.size()) {
            smallHeap.offer(largeHeap.poll());
        }
    }
    
    public double findMedian() {
        if (smallHeap.size() > largeHeap.size()) {
            return smallHeap.peek();
        }
        return (smallHeap.peek() + largeHeap.peek()) / 2.0;
    }
}
```
**Độ phức tạp**:
- Time: `addNum` $O(\log N)$, `findMedian` $O(1)$.
- Space: $O(N)$ để lưu tất cả các phần tử vào Heap.

## 21. K-th Smallest Prime Fraction (LeetCode 786)
**Đề bài chi tiết**: Cho mảng `arr` chứa các số nguyên tố tăng dần và số 1. Một phân số được tạo thành từ 2 số `arr[i] / arr[j]` với `0 <= i < j < arr.length`. Trả về phân số nhỏ thứ `k` dưới dạng mảng 2 phần tử `[arr[i], arr[j]]`.
**Phân tích thuật toán**: Dùng hàng đợi ưu tiên (Min-Heap) để lưu các phân số `arr[i] / arr[j]`. Ban đầu thêm các phân số `arr[0] / arr[j]` vào Heap (vì mảng tăng dần, `arr[0]` chia các số lớn nhất sẽ là các phân số nhỏ nhất). Mỗi lần pop một phân số nhỏ nhất ra, ta thêm phân số tiếp theo `arr[i+1] / arr[j]` vào Heap. Lặp lại quá trình này `k - 1` lần, phần tử trên đỉnh Heap sau đó sẽ là phần tử nhỏ thứ `k`. (Ngoài ra có thể dùng Binary Search).
**Mã nguồn Java**:
```java
import java.util.PriorityQueue;

public class Solution {
    public int[] kthSmallestPrimeFraction(int[] arr, int k) {
        PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> {
            // so sánh a[0]/a[1] và b[0]/b[1] -> so sánh a[0]*b[1] và b[0]*a[1]
            return arr[a[0]] * arr[b[1]] - arr[b[0]] * arr[a[1]];
        });
        
        for (int j = 1; j < arr.length; j++) {
            minHeap.offer(new int[]{0, j});
        }
        
        for (int i = 0; i < k - 1; i++) {
            int[] fraction = minHeap.poll();
            int numIdx = fraction[0];
            int denIdx = fraction[1];
            
            if (numIdx + 1 < denIdx) {
                minHeap.offer(new int[]{numIdx + 1, denIdx});
            }
        }
        
        int[] result = minHeap.poll();
        return new int[]{arr[result[0]], arr[result[1]]};
    }
}
```
**Độ phức tạp**:
- Time: $O(N + K \log N)$.
- Space: $O(N)$ lưu trữ trong Heap.

## 22. Rearrange Array Elements by Sign (LeetCode 2149)
**Đề bài chi tiết**: Cho mảng nguyên `nums` có số lượng số dương bằng số lượng số âm. Sắp xếp lại các phần tử sao cho chúng xen kẽ nhau (bắt đầu bằng số dương) và giữ nguyên thứ tự xuất hiện ban đầu của các số dương và âm.
**Phân tích thuật toán**: Khởi tạo 2 con trỏ, một chỉ vị trí của số dương tiếp theo (bắt đầu từ 0) và một chỉ vị trí của số âm tiếp theo (bắt đầu từ 1). Duyệt qua mảng ban đầu, gặp số dương thì điền vào vị trí chẵn (và tăng lên 2), gặp số âm thì điền vào vị trí lẻ (và tăng lên 2). Cần một mảng phụ để lưu kết quả.
**Mã nguồn Java**:
```java
public class Solution {
    public int[] rearrangeArray(int[] nums) {
        int[] res = new int[nums.length];
        int posIdx = 0;
        int negIdx = 1;
        
        for (int num : nums) {
            if (num > 0) {
                res[posIdx] = num;
                posIdx += 2;
            } else {
                res[negIdx] = num;
                negIdx += 2;
            }
        }
        return res;
    }
}
```
**Độ phức tạp**:
- Time: $O(N)$ do duyệt một vòng.
- Space: $O(N)$ tạo mảng kết quả mới.

## 23. Partition Array According to Given Pivot (LeetCode 2161)
**Đề bài chi tiết**: Cho mảng số nguyên `nums` và một giá trị `pivot`. Phân hoạch mảng sao cho: Các số nhỏ hơn `pivot` đứng trước; các số bằng `pivot` ở giữa; các số lớn hơn `pivot` đứng cuối. Đồng thời phải giữ nguyên thứ tự tương đối của các số trong mỗi nhóm.
**Phân tích thuật toán**: Có thể sử dụng ba danh sách hoặc sử dụng mảng phụ kết hợp 2 lần duyệt. Tuy nhiên, để tối ưu in-place một phần hoặc gộp lại, ta duyệt một lần từ đầu mảng để xếp các số nhỏ hơn và đếm số phần tử bằng `pivot`. Duyệt từ cuối mảng (hoặc dùng con trỏ) để điền các số lớn hơn, hoặc đơn giản nhất là duyệt mảng và lưu vào 3 list, sau đó gộp lại. Phương pháp tối ưu nhất là dùng mảng kết quả và 2 con trỏ ở hai đầu.
**Mã nguồn Java**:
```java
public class Solution {
    public int[] pivotArray(int[] nums, int pivot) {
        int[] res = new int[nums.length];
        int left = 0;
        int right = nums.length - 1;
        
        for (int i = 0, j = nums.length - 1; i < nums.length; i++, j--) {
            if (nums[i] < pivot) {
                res[left++] = nums[i];
            }
            if (nums[j] > pivot) {
                res[right--] = nums[j];
            }
        }
        
        while (left <= right) {
            res[left++] = pivot;
        }
        
        return res;
    }
}
```
**Độ phức tạp**:
- Time: $O(N)$ do duyệt mảng.
- Space: $O(N)$ cho mảng kết quả mới (bắt buộc vì yêu cầu Stable partition thường không thể $O(1)$ space với $O(N)$ time).

## 24. Maximize the Top 2 Elements Sum
**Đề bài chi tiết**: Cho một mảng các số nguyên dương `nums`, tìm 2 phần tử lớn nhất và lớn thứ hai trong mảng. Trả về tổng của chúng.
**Phân tích thuật toán**: Đây là một bài toán Selection đơn giản. Thay vì sắp xếp mảng mất $O(N \log N)$, ta có thể duy trì hai biến `max1` và `max2` và duyệt qua mảng một lần để tìm. Nếu phần tử hiện tại lớn hơn `max1`, ta cập nhật `max2 = max1` và `max1 = current`. Nếu lớn hơn `max2` nhưng nhỏ hơn hoặc bằng `max1`, cập nhật `max2 = current`.
**Mã nguồn Java**:
```java
public class Solution {
    public int maxTwoSum(int[] nums) {
        int max1 = Integer.MIN_VALUE;
        int max2 = Integer.MIN_VALUE;
        
        for (int num : nums) {
            if (num > max1) {
                max2 = max1;
                max1 = num;
            } else if (num > max2) {
                max2 = num;
            }
        }
        
        return max1 + max2;
    }
}
```
**Độ phức tạp**:
- Time: $O(N)$ duyệt 1 vòng.
- Space: $O(1)$.

## 25. Distribute Candies (LeetCode 575)
**Đề bài chi tiết**: Alice có `n` viên kẹo, viên thứ `i` có loại kẹo là `candyType[i]`. Cô ấy chỉ được phép ăn tối đa `n / 2` viên kẹo. Tìm số lượng tối đa các loại kẹo khác nhau mà Alice có thể ăn.
**Phân tích thuật toán**: Thuật toán Selection (chọn lọc tập hợp). Sử dụng HashSet để tìm ra tổng số lượng loại kẹo phân biệt trong mảng. Số loại kẹo tối đa Alice có thể ăn chính là giá trị nhỏ hơn giữa số loại kẹo phân biệt và nửa số lượng kẹo (`n / 2`).
**Mã nguồn Java**:
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int distributeCandies(int[] candyType) {
        Set<Integer> uniqueCandies = new HashSet<>();
        for (int candy : candyType) {
            uniqueCandies.add(candy);
        }
        
        return Math.min(uniqueCandies.size(), candyType.length / 2);
    }
}
```
**Độ phức tạp**:
- Time: $O(N)$ duyệt qua mảng và thêm vào HashSet.
- Space: $O(N)$ để lưu HashSet.

## 26. K-th Smallest Element in a Sorted Matrix (LeetCode 378)
**Đề bài chi tiết**: Cho một ma trận `n x n` tên `matrix` trong đó mỗi hàng và mỗi cột đều được sắp xếp theo thứ tự tăng dần. Trả về phần tử nhỏ thứ `k` trong ma trận.
**Phân tích thuật toán**: Có hai cách tiếp cận chính:
1. Dùng Min-Heap kích thước K lưu trữ tọa độ $(r, c)$ và duyệt tương tự K-way merge.
2. Tìm kiếm nhị phân (Binary Search) trên khoảng giá trị: `left = matrix[0][0]` và `right = matrix[n-1][n-1]`. Đếm số phần tử $\le mid$, nếu $\ge k$ thì `right = mid`, ngược lại `left = mid + 1`.
**Mã nguồn Java**:
```java
public class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int left = matrix[0][0];
        int right = matrix[n - 1][n - 1];
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            int count = countLessOrEqual(matrix, mid);
            
            if (count >= k) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }
    
    private int countLessOrEqual(int[][] matrix, int target) {
        int count = 0;
        int n = matrix.length;
        int r = n - 1;
        int c = 0;
        
        while (r >= 0 && c < n) {
            if (matrix[r][c] <= target) {
                count += r + 1;
                c++;
            } else {
                r--;
            }
        }
        return count;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \log(\text{Max} - \text{Min}))$ do tìm kiếm nhị phân và đếm trong $O(N)$.
- Space: $O(1)$.

## 27. Partition Array into Disjoint Intervals (LeetCode 915)
**Đề bài chi tiết**: Cho một mảng `nums`, phân hoạch thành 2 phần liên tiếp `left` và `right` sao cho: Mọi phần tử trong `left` đều nhỏ hơn hoặc bằng mọi phần tử trong `right`, `left` và `right` không rỗng, và `left` có kích thước nhỏ nhất có thể. Trả về chiều dài của `left`.
**Phân tích thuật toán**: Ta cần tìm một chỉ số `i` sao cho giá trị lớn nhất từ `0` đến `i` (gọi là `maxLeft`) nhỏ hơn hoặc bằng mọi giá trị từ `i+1` đến `n-1`. Ta có thể duyệt một vòng để cập nhật `maxLeft` cho đoạn `left` hiện tại. Nếu gặp một phần tử `nums[i] < maxLeft`, bắt buộc phần tử này phải thuộc `left`, nên ta mở rộng `left` ra tới `i`, đồng thời cập nhật lại `maxLeft` mới bằng giá trị max tính từ đầu mảng tới `i`.
**Mã nguồn Java**:
```java
public class Solution {
    public int partitionDisjoint(int[] nums) {
        int currentMax = nums[0]; // Max của subarray left
        int possibleMax = nums[0]; // Max từ đầu đến i
        int partitionLength = 1;
        
        for (int i = 1; i < nums.length; i++) {
            possibleMax = Math.max(possibleMax, nums[i]);
            if (nums[i] < currentMax) {
                currentMax = possibleMax;
                partitionLength = i + 1;
            }
        }
        
        return partitionLength;
    }
}
```
**Độ phức tạp**:
- Time: $O(N)$ chỉ một vòng duyệt.
- Space: $O(1)$.

## 28. Sort an Array (LeetCode 912)
**Đề bài chi tiết**: Cho mảng số nguyên `nums`, sắp xếp mảng theo thứ tự tăng dần. Không được sử dụng các hàm sắp xếp tích hợp sẵn. Yêu cầu Time Complexity $O(N \log N)$ cho Worst-case.
**Phân tích thuật toán**: Có thể sử dụng Merge Sort, Heap Sort, hoặc Quick Sort (với Randomized Pivot). Dưới đây trình bày **Merge Sort** do nó đảm bảo ổn định $O(N \log N)$ trong mọi trường hợp (worst, average, best).
**Mã nguồn Java**:
```java
public class Solution {
    public int[] sortArray(int[] nums) {
        mergeSort(nums, 0, nums.length - 1);
        return nums;
    }
    
    private void mergeSort(int[] nums, int left, int right) {
        if (left >= right) return;
        int mid = left + (right - left) / 2;
        
        mergeSort(nums, left, mid);
        mergeSort(nums, mid + 1, right);
        merge(nums, left, mid, right);
    }
    
    private void merge(int[] nums, int left, int mid, int right) {
        int[] temp = new int[right - left + 1];
        int i = left, j = mid + 1, k = 0;
        
        while (i <= mid && j <= right) {
            if (nums[i] <= nums[j]) temp[k++] = nums[i++];
            else temp[k++] = nums[j++];
        }
        
        while (i <= mid) temp[k++] = nums[i++];
        while (j <= right) temp[k++] = nums[j++];
        
        for (int p = 0; p < temp.length; p++) {
            nums[left + p] = temp[p];
        }
    }
}
```
**Độ phức tạp**:
- Time: $O(N \log N)$.
- Space: $O(N)$ cho mảng tạm `temp`.

## 29. Count of Smaller Numbers After Self (LeetCode 315)
**Đề bài chi tiết**: Cho mảng số nguyên `nums`, trả về mảng số nguyên `counts` trong đó `counts[i]` là số lượng các phần tử nằm bên phải của `nums[i]` và nhỏ hơn `nums[i]`.
**Phân tích thuật toán**: Bài toán nâng cao sử dụng chia để trị (Divide and Conquer), đặc biệt là biến thể của Merge Sort. Trong quá trình trộn hai nửa đã sắp xếp, mỗi khi một phần tử ở nửa trái lớn hơn phần tử ở nửa phải, tất cả các phần tử còn lại trong nửa phải sẽ nhỏ hơn phần tử đó ở nửa trái. Cần lưu lại chỉ số gốc của các phần tử để cập nhật vào mảng kết quả.
**Mã nguồn Java**:
```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    int[] count;
    
    public List<Integer> countSmaller(int[] nums) {
        int n = nums.length;
        count = new int[n];
        int[] indices = new int[n];
        for (int i = 0; i < n; i++) indices[i] = i;
        
        mergeSort(nums, indices, 0, n - 1);
        
        List<Integer> res = new ArrayList<>();
        for (int c : count) res.add(c);
        return res;
    }
    
    private void mergeSort(int[] nums, int[] indices, int left, int right) {
        if (left >= right) return;
        int mid = left + (right - left) / 2;
        mergeSort(nums, indices, left, mid);
        mergeSort(nums, indices, mid + 1, right);
        merge(nums, indices, left, mid, right);
    }
    
    private void merge(int[] nums, int[] indices, int left, int mid, int right) {
        int[] tempIndices = new int[right - left + 1];
        int i = left, j = mid + 1, k = 0;
        int rightCount = 0;
        
        while (i <= mid && j <= right) {
            if (nums[indices[j]] < nums[indices[i]]) {
                rightCount++;
                tempIndices[k++] = indices[j++];
            } else {
                count[indices[i]] += rightCount;
                tempIndices[k++] = indices[i++];
            }
        }
        
        while (i <= mid) {
            count[indices[i]] += rightCount;
            tempIndices[k++] = indices[i++];
        }
        while (j <= right) {
            tempIndices[k++] = indices[j++];
        }
        
        for (int p = 0; p < tempIndices.length; p++) {
            indices[left + p] = tempIndices[p];
        }
    }
}
```
**Độ phức tạp**:
- Time: $O(N \log N)$.
- Space: $O(N)$ lưu trữ mảng chỉ số và kết quả đếm.

## 30. Maximum Gap (LeetCode 164)
**Đề bài chi tiết**: Cho mảng số nguyên `nums` chưa được sắp xếp, trả về chênh lệch tối đa giữa hai phần tử liên tiếp nếu mảng được sắp xếp. Yêu cầu thời gian tuyến tính $O(N)$.
**Phân tích thuật toán**: Thuật toán Bucket Sort kết hợp nguyên lý chuồng bồ câu (Pigeonhole Principle). Tính giá trị `min` và `max` của mảng. Chênh lệch tối đa ít nhất phải bằng `gap = ceil((max - min) / (N - 1))`. Do đó ta có thể chia khoảng giá trị thành các bucket có độ rộng `gap`. Ta chỉ cần theo dõi `min` và `max` trong mỗi bucket. Khoảng cách lớn nhất sẽ xuất hiện giữa `min` của một bucket hiện tại và `max` của bucket có phần tử trước nó.
**Mã nguồn Java**:
```java
import java.util.Arrays;

public class Solution {
    public int maximumGap(int[] nums) {
        if (nums == null || nums.length < 2) return 0;
        
        int n = nums.length;
        int min = nums[0], max = nums[0];
        for (int num : nums) {
            min = Math.min(min, num);
            max = Math.max(max, num);
        }
        if (min == max) return 0;
        
        int gap = (int) Math.ceil((double)(max - min) / (n - 1));
        int[] bucketMin = new int[n];
        int[] bucketMax = new int[n];
        Arrays.fill(bucketMin, Integer.MAX_VALUE);
        Arrays.fill(bucketMax, Integer.MIN_VALUE);
        
        for (int num : nums) {
            int idx = (num - min) / gap;
            bucketMin[idx] = Math.min(bucketMin[idx], num);
            bucketMax[idx] = Math.max(bucketMax[idx], num);
        }
        
        int maxGap = 0;
        int prevMax = bucketMax[0];
        for (int i = 1; i < n; i++) {
            if (bucketMin[i] == Integer.MAX_VALUE) continue;
            maxGap = Math.max(maxGap, bucketMin[i] - prevMax);
            prevMax = bucketMax[i];
        }
        
        return maxGap;
    }
}
```
**Độ phức tạp**:
- Time: $O(N)$ do duyệt mảng cố định số vòng.
- Space: $O(N)$ lưu 2 mảng `bucketMin` và `bucketMax`.
