# Bài tập Quick Sort & Quick Select (30 bài tập)

Dưới đây là 30 bài toán đa dạng áp dụng thuật toán Quick Sort, thuật toán chọn nhanh (Quick Select), và kỹ thuật phân vùng (Partitioning).

---

## 1. Sort an Array (Quick Sort)
**Đề bài chi tiết:** Cho một mảng số nguyên `nums`, hãy sắp xếp mảng theo thứ tự tăng dần và trả về. Cố gắng sử dụng giải thuật sắp xếp với độ phức tạp $O(N \log N)$ và yêu cầu bộ nhớ nhỏ nhất có thể.
**Phân tích thuật toán:** Bài này là kinh điển để cài đặt trực tiếp Quick Sort. Để tránh TLE (Time Limit Exceeded) trên các test case có mảng đã sắp xếp hoặc có phần tử trùng lặp, chúng ta sử dụng Randomized Quick Sort (chọn Pivot ngẫu nhiên) hoặc Pivot trung vị.
**Mã nguồn Java:**
```java
class Solution {
    public int[] sortArray(int[] nums) {
        if(nums == null || nums.length == 0) return nums;
        quickSort(nums, 0, nums.length - 1);
        return nums;
    }
    
    private void quickSort(int[] nums, int left, int right) {
        if(left >= right) return;
        int pivotIndex = partition(nums, left, right);
        quickSort(nums, left, pivotIndex - 1);
        quickSort(nums, pivotIndex + 1, right);
    }
    
    private int partition(int[] nums, int left, int right) {
        // Randomized pivot
        int randomIndex = left + (int)(Math.random() * (right - left + 1));
        swap(nums, randomIndex, right);
        
        int pivot = nums[right];
        int i = left - 1;
        for(int j = left; j < right; j++) {
            if(nums[j] < pivot) {
                i++;
                swap(nums, i, j);
            }
        }
        swap(nums, i + 1, right);
        return i + 1;
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
    }
}
```
**Độ phức tạp:**
- Time: Average $O(N \log N)$, Worst $O(N^2)$.
- Space: $O(\log N)$ trên recursion stack.

---

## 2. Kth Largest Element in an Array (Quick Select)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` và số nguyên `k`, trả về phần tử lớn thứ `k` trong mảng. Yêu cầu giải quyết trong thời gian trung bình $O(N)$.
**Phân tích thuật toán:** Thay vì sắp xếp toàn bộ mảng mất $O(N \log N)$, ta có thể sử dụng thuật toán Quick Select. Chúng ta sử dụng quá trình Partition. Phần tử lớn thứ K tương đương với phần tử đứng ở chỉ số `N - k` khi mảng được sắp xếp tăng dần. Nếu pivot đỗ ở đúng vị trí `N - k`, ta đã tìm thấy kết quả. Nếu không, tiếp tục chia để trị trên nửa mảng thích hợp.
**Mã nguồn Java:**
```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        int n = nums.length;
        int targetIndex = n - k;
        return quickSelect(nums, 0, n - 1, targetIndex);
    }
    
    private int quickSelect(int[] nums, int left, int right, int target) {
        if (left == right) return nums[left];
        
        int pivotIndex = partition(nums, left, right);
        if (pivotIndex == target) {
            return nums[pivotIndex];
        } else if (pivotIndex < target) {
            return quickSelect(nums, pivotIndex + 1, right, target);
        } else {
            return quickSelect(nums, left, pivotIndex - 1, target);
        }
    }
    
    private int partition(int[] nums, int left, int right) {
        int randomIndex = left + (int)(Math.random() * (right - left + 1));
        swap(nums, randomIndex, right);
        
        int pivot = nums[right];
        int i = left;
        for(int j = left; j < right; j++) {
            if(nums[j] < pivot) {
                swap(nums, i, j);
                i++;
            }
        }
        swap(nums, i, right);
        return i;
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
    }
}
```
**Độ phức tạp:**
- Time: Average $O(N)$, Worst $O(N^2)$.
- Space: $O(\log N)$ đệ quy (hoặc $O(1)$ nếu làm Iterative).

---

## 3. Sort Colors (Dutch National Flag)
**Đề bài chi tiết:** Cho mảng `nums` gồm $n$ phần tử, mỗi phần tử có giá trị là 0, 1 hoặc 2 (tương ứng với đỏ, trắng, xanh). Hãy sắp xếp các đối tượng cùng màu đứng gần nhau và theo thứ tự Đỏ, Trắng, Xanh. Giải thuật In-place với Time Complexity $O(N)$.
**Phân tích thuật toán:** Sử dụng bài toán "Cờ Hà Lan" (Dutch National Flag problem) được sáng tạo bởi Edsger W. Dijkstra. Sử dụng 3 con trỏ: `low`, `mid`, và `high`. `low` gom các số 0, `high` gom các số 2, và `mid` dùng để duyệt. Thuật toán này chính là kỹ thuật chia 3 phần trong Quick Sort (3-way partitioning).
**Mã nguồn Java:**
```java
class Solution {
    public void sortColors(int[] nums) {
        int low = 0, mid = 0, high = nums.length - 1;
        
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
        int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
    }
}
```
**Độ phức tạp:**
- Time: $O(N)$
- Space: $O(1)$ In-place.

---

## 4. Top K Frequent Elements
**Đề bài chi tiết:** Cho một mảng nguyên `nums` và một số nguyên `k`, trả về `k` phần tử xuất hiện nhiều lần nhất trong mảng.
**Phân tích thuật toán:** Dùng HashMap để đếm tần số xuất hiện của từng phần tử. Sau đó, lưu các cặp `(phần tử, tần số)` vào mảng hoặc danh sách. Áp dụng Quick Select trên danh sách các khóa đó dựa vào "tần số" để tìm `k` phần tử phổ biến nhất, tương tự cách tìm phần tử lớn thứ k.
**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freqMap = new HashMap<>();
        for (int num : nums) {
            freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);
        }
        
        int[] unique = new int[freqMap.size()];
        int idx = 0;
        for (int num : freqMap.keySet()) {
            unique[idx++] = num;
        }
        
        quickSelect(unique, freqMap, 0, unique.length - 1, unique.length - k);
        return Arrays.copyOfRange(unique, unique.length - k, unique.length);
    }
    
    private void quickSelect(int[] unique, Map<Integer, Integer> map, int left, int right, int kSmallest) {
        if (left >= right) return;
        
        int pivotIndex = partition(unique, map, left, right);
        if (pivotIndex == kSmallest) {
            return;
        } else if (pivotIndex < kSmallest) {
            quickSelect(unique, map, pivotIndex + 1, right, kSmallest);
        } else {
            quickSelect(unique, map, left, pivotIndex - 1, kSmallest);
        }
    }
    
    private int partition(int[] unique, Map<Integer, Integer> map, int left, int right) {
        int pivotFreq = map.get(unique[right]);
        int i = left;
        for (int j = left; j < right; j++) {
            if (map.get(unique[j]) < pivotFreq) {
                swap(unique, i, j);
                i++;
            }
        }
        swap(unique, i, right);
        return i;
    }
    
    private void swap(int[] arr, int i, int j) {
        int temp = arr[i]; arr[i] = arr[j]; arr[j] = temp;
    }
}
```
**Độ phức tạp:**
- Time: Average $O(N)$, trong đó đếm phần tử tốn $O(N)$ và Quick Select tốn $O(U)$ với U là số phần tử duy nhất.
- Space: $O(N)$ cho HashMap.

---

## 5. Wiggle Sort II
**Đề bài chi tiết:** Cho một mảng `nums`, sắp xếp lại theo thứ tự `nums[0] < nums[1] > nums[2] < nums[3]...`. Yêu cầu giải thuật $O(N)$ In-place hoặc $O(N \log N)$ space nhỏ.
**Phân tích thuật toán:** Bài toán có thể giải quyết gọn gàng bằng cách tìm phần tử trung vị (Median) sử dụng Quick Select ($O(N)$ time). Dựa vào giá trị trung vị, ta dùng ý tưởng phân vùng 3 phần (Dutch National Flag) cùng kỹ thuật "Virtual Indexing" (chỉ số ảo) để đưa các phần tử lớn hơn vào vị trí lẻ và nhỏ hơn vào vị trí chẵn. Mã nguồn dưới đây sử dụng $O(N \log N)$ cơ bản do $O(N)$ khá phức tạp để viết ngắn.
**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public void wiggleSort(int[] nums) {
        int[] copy = nums.clone();
        Arrays.sort(copy); // Có thể thay bằng Quick Sort
        
        int n = nums.length;
        int left = (n + 1) / 2 - 1; // Median index
        int right = n - 1;          // Largest index
        
        for (int i = 0; i < n; i++) {
            if (i % 2 == 0) {
                nums[i] = copy[left--];
            } else {
                nums[i] = copy[right--];
            }
        }
    }
}
```
*(Ghi chú: Bản $O(N)$ yêu cầu Quick Select + 3-way partition + Virtual Indexing, bạn có thể tự thử thách bản thân mở rộng mã này).*
**Độ phức tạp:**
- Time: $O(N \log N)$ cho cách sorting, $O(N)$ cho cách Quick Select (thời gian trung bình).
- Space: $O(N)$ tạo mảng copy.

---

## 6. K Closest Points to Origin
**Đề bài chi tiết:** Cho một mảng các điểm `points` trong mặt phẳng 2D và số `k`, hãy tìm `k` điểm gần gốc toạ độ `(0, 0)` nhất. Khoảng cách tính bằng khoảng cách Euclid.
**Phân tích thuật toán:** Áp dụng thuật toán Quick Select. Chúng ta định nghĩa khoảng cách của điểm `(x, y)` tới gốc là `x*x + y*y`. Chúng ta cần tìm $K$ phần tử nhỏ nhất dựa trên so sánh khoảng cách này.
**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int[][] kClosest(int[][] points, int k) {
        quickSelect(points, 0, points.length - 1, k);
        return Arrays.copyOfRange(points, 0, k);
    }
    
    private void quickSelect(int[][] points, int left, int right, int k) {
        if (left >= right) return;
        
        int pivotIndex = partition(points, left, right);
        if (pivotIndex == k) {
            return;
        } else if (pivotIndex < k) {
            quickSelect(points, pivotIndex + 1, right, k);
        } else {
            quickSelect(points, left, pivotIndex - 1, k);
        }
    }
    
    private int partition(int[][] points, int left, int right) {
        int[] pivot = points[right];
        int pivotDist = dist(pivot);
        int i = left;
        
        for (int j = left; j < right; j++) {
            if (dist(points[j]) < pivotDist) {
                swap(points, i, j);
                i++;
            }
        }
        swap(points, i, right);
        return i;
    }
    
    private int dist(int[] point) {
        return point[0]*point[0] + point[1]*point[1];
    }
    
    private void swap(int[][] points, int i, int j) {
        int[] temp = points[i]; points[i] = points[j]; points[j] = temp;
    }
}
```
**Độ phức tạp:**
- Time: Average $O(N)$, Worst $O(N^2)$.
- Space: $O(\log N)$ đệ quy.

---

## 7. Pancake Sorting
**Đề bài chi tiết:** Cho mảng `arr`, hãy sắp xếp mảng thông qua một loạt thao tác lật bánh pancake (đảo ngược mảng con từ chỉ số 0 đến k). Trả về danh sách độ dài `k` các lần đảo ngược để mảng được sắp xếp.
**Phân tích thuật toán:** Thuật toán này không dùng thẳng Quick Sort nhưng về cơ bản nó tìm phần tử lớn nhất, lật nó lên đầu, rồi lật nó xuống vị trí cuối cùng của vùng chưa sắp xếp, từ đó dần thu hẹp kích thước mảng tương tự việc chia vùng partition.
**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public List<Integer> pancakeSort(int[] arr) {
        List<Integer> res = new ArrayList<>();
        for (int n = arr.length; n > 0; n--) {
            int index = find(arr, n);
            if (index == n - 1) continue;
            
            if (index != 0) {
                res.add(index + 1);
                flip(arr, index + 1);
            }
            res.add(n);
            flip(arr, n);
        }
        return res;
    }
    
    private int find(int[] arr, int target) {
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] == target) return i;
        }
        return -1;
    }
    
    private void flip(int[] arr, int k) {
        int i = 0, j = k - 1;
        while (i < j) {
            int temp = arr[i]; arr[i] = arr[j]; arr[j] = temp;
            i++; j--;
        }
    }
}
```
**Độ phức tạp:**
- Time: $O(N^2)$ (tìm và lật mảng).
- Space: $O(N)$ lưu output list.

---

## 8. Maximum Number of Coins You Can Get
**Đề bài chi tiết:** Có `3n` cọc tiền xu với các số lượng khác nhau. Bạn, Alice và Bob lần lượt chọn 3 cọc xu bất kỳ. Alice luôn lấy cọc nhiều xu nhất, bạn lấy cọc thứ hai, và Bob lấy cọc nhỏ nhất. Tính tổng xu tối đa bạn thu được.
**Phân tích thuật toán:** Chiến lược tối ưu (Greedy) là sắp xếp mảng. Bob lấy 1/3 nhỏ nhất. Ta và Alice chia nhau 2/3 lớn nhất, trong đó cứ mỗi 2 phần tử ta lấy phần tử nhỏ hơn. Quick Sort giải quyết bài này nhanh gọn.
**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int maxCoins(int[] piles) {
        // Quick Sort Inbuilt
        Arrays.sort(piles);
        int res = 0;
        int n = piles.length;
        // Bob lấy piles.length / 3 phần tử ở đầu
        for (int i = n / 3; i < n; i += 2) {
            res += piles[i];
        }
        return res;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$.
- Space: $O(\log N)$ tuỳ ngôn ngữ.

---

## 9. The K Weakest Rows in a Matrix
**Đề bài chi tiết:** Cho một ma trận nhị phân `mat` cỡ `m x n` (với 1 tượng trưng cho lính và 0 cho dân thường), các lính luôn đứng trước dân thường. Hàng nào ít lính hơn hoặc số lính bằng nhau nhưng chỉ số hàng nhỏ hơn thì hàng đó yếu hơn. Tìm chỉ số `k` hàng yếu nhất.
**Phân tích thuật toán:** Đếm số lính (Binary Search do hàng đã sắp xếp 1 rồi tới 0), sau đó lưu danh sách `[số lính, chỉ số hàng]`. Dùng Quick Select hoặc Quick Sort để lấy ra `k` phần tử đầu tiên.
**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int[] kWeakestRows(int[][] mat, int k) {
        int m = mat.length;
        int[][] soldiers = new int[m][2];
        
        for (int i = 0; i < m; i++) {
            soldiers[i][0] = countOnes(mat[i]);
            soldiers[i][1] = i;
        }
        
        // Sorting using custom comparator (Quick Sort behind the scenes)
        Arrays.sort(soldiers, (a, b) -> {
            if (a[0] == b[0]) return a[1] - b[1];
            else return a[0] - b[0];
        });
        
        int[] res = new int[k];
        for (int i = 0; i < k; i++) {
            res[i] = soldiers[i][1];
        }
        return res;
    }
    
    private int countOnes(int[] row) {
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
**Độ phức tạp:**
- Time: $O(M \log N)$ đếm lính + $O(M \log M)$ sắp xếp = $O(M (\log N + \log M))$.
- Space: $O(M)$ để lưu mảng trung gian.

---

## 10. Minimize Maximum Pair Sum in Array
**Đề bài chi tiết:** Cho một mảng `nums` chẵn phần tử, ghép các phần tử thành các cặp sao cho tổng của giá trị lớn nhất trong các cặp là nhỏ nhất có thể. Trả về giá trị tổng lớn nhất đó.
**Phân tích thuật toán:** Để tối thiểu hoá cực đại (minimize maximum), ta cần ghép số lớn nhất với số nhỏ nhất, số lớn nhì với số nhỏ nhì... Để làm vậy, sắp xếp mảng bằng Quick Sort, sau đó dùng 2 con trỏ, một ở đầu và một ở cuối tiến vào giữa, tính tổng các cặp và tìm GTLN.
**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int minPairSum(int[] nums) {
        Arrays.sort(nums); // Áp dụng Quick Sort
        
        int max = 0;
        int left = 0;
        int right = nums.length - 1;
        
        while (left < right) {
            max = Math.max(max, nums[left] + nums[right]);
            left++;
            right--;
        }
        return max;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$ (vì thao tác hai con trỏ là $O(N)$ sau khi sắp xếp).
- Space: $O(\log N)$ (Call stack).

---

## 11. Majority Element (Quick Select Approach)
**Đề bài chi tiết:** Cho một mảng `nums` kích thước `n`, tìm phần tử đa số (phần tử xuất hiện nhiều hơn `⌊n / 2⌋` lần). Bạn có thể giả định rằng phần tử đa số luôn tồn tại trong mảng.
**Phân tích thuật toán:** Mặc dù Boyer-Moore Voting Algorithm là tối ưu nhất cho bài này, nhưng ta có thể áp dụng thuật toán Quick Select. Vì phần tử đa số chiếm hơn một nửa số lượng phần tử, nếu ta sắp xếp mảng, nó chắc chắn sẽ nằm ở vị trí `n/2`. Do đó, ta chỉ cần dùng Quick Select để tìm phần tử lớn thứ `n/2` (hoặc phần tử ở chỉ số `n/2` khi mảng được sắp xếp).
**Mã nguồn Java:**
```java
class Solution {
    public int majorityElement(int[] nums) {
        return quickSelect(nums, 0, nums.length - 1, nums.length / 2);
    }
    
    private int quickSelect(int[] nums, int left, int right, int k) {
        if (left == right) return nums[left];
        
        int pivotIndex = partition(nums, left, right);
        if (pivotIndex == k) {
            return nums[pivotIndex];
        } else if (pivotIndex < k) {
            return quickSelect(nums, pivotIndex + 1, right, k);
        } else {
            return quickSelect(nums, left, pivotIndex - 1, k);
        }
    }
    
    private int partition(int[] nums, int left, int right) {
        int randomIndex = left + (int)(Math.random() * (right - left + 1));
        swap(nums, randomIndex, right);
        
        int pivot = nums[right];
        int i = left;
        for (int j = left; j < right; j++) {
            if (nums[j] < pivot) {
                swap(nums, i, j);
                i++;
            }
        }
        swap(nums, i, right);
        return i;
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
    }
}
```
**Độ phức tạp:**
- Time: Average $O(N)$, Worst $O(N^2)$.
- Space: $O(\log N)$ đệ quy.

---

## 12. Kth Smallest Element in a Sorted Matrix
**Đề bài chi tiết:** Cho một ma trận `matrix` kích thước `n x n` trong đó mỗi hàng và mỗi cột đều được sắp xếp theo thứ tự tăng dần, trả về phần tử nhỏ thứ `k` trong ma trận.
**Phân tích thuật toán:** Một cách đơn giản là đưa tất cả các phần tử của ma trận vào một mảng 1D, sau đó áp dụng Quick Select để tìm phần tử nhỏ thứ `k` trong $O(N^2)$ thời gian trung bình. Tuy nhiên, cách này không tận dụng được tính chất đã sắp xếp của ma trận (Binary Search sẽ tối ưu hơn với $O(N \log(Max - Min))$). Ở đây ta minh họa Quick Select sau khi flatten mảng để thể hiện kỹ thuật áp dụng.
**Mã nguồn Java:**
```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int[] arr = new int[n * n];
        int idx = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                arr[idx++] = matrix[i][j];
            }
        }
        return quickSelect(arr, 0, arr.length - 1, k - 1);
    }
    
    private int quickSelect(int[] arr, int left, int right, int k) {
        if (left == right) return arr[left];
        
        int pivotIndex = partition(arr, left, right);
        if (pivotIndex == k) {
            return arr[pivotIndex];
        } else if (pivotIndex < k) {
            return quickSelect(arr, pivotIndex + 1, right, k);
        } else {
            return quickSelect(arr, left, pivotIndex - 1, k);
        }
    }
    
    private int partition(int[] arr, int left, int right) {
        int pivot = arr[right];
        int i = left;
        for (int j = left; j < right; j++) {
            if (arr[j] < pivot) {
                swap(arr, i, j);
                i++;
            }
        }
        swap(arr, i, right);
        return i;
    }
    
    private void swap(int[] arr, int i, int j) {
        int temp = arr[i]; arr[i] = arr[j]; arr[j] = temp;
    }
}
```
**Độ phức tạp:**
- Time: Average $O(V)$ với $V = N^2$, Worst $O(V^2)$.
- Space: $O(V)$ tạo mảng 1D.

---

## 13. Sort List
**Đề bài chi tiết:** Cho phần tử đầu của một danh sách liên kết `head`, hãy sắp xếp danh sách liên kết đó theo thứ tự tăng dần và trả về. Giải thuật nên đạt độ phức tạp $O(N \log N)$.
**Phân tích thuật toán:** Merge Sort là giải thuật phổ biến nhất cho danh sách liên kết. Tuy nhiên, ta hoàn toàn có thể triển khai Quick Sort trên danh sách liên kết. Ta chọn một `pivot` (ví dụ phần tử đầu), chia danh sách thành hai danh sách con: một chứa các phần tử nhỏ hơn `pivot` và một chứa các phần tử lớn hơn hoặc bằng `pivot`. Gọi đệ quy trên hai danh sách con và nối chúng lại với nhau cùng với `pivot`.
**Mã nguồn Java:**
```java
class Solution {
    public class ListNode {
        int val;
        ListNode next;
        ListNode(int x) { val = x; }
    }

    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) return head;
        
        ListNode pivot = head;
        ListNode smallerHead = new ListNode(0), smaller = smallerHead;
        ListNode greaterHead = new ListNode(0), greater = greaterHead;
        ListNode equalHead = new ListNode(0), equal = equalHead;
        
        ListNode curr = head;
        while (curr != null) {
            if (curr.val < pivot.val) {
                smaller.next = curr;
                smaller = smaller.next;
            } else if (curr.val > pivot.val) {
                greater.next = curr;
                greater = greater.next;
            } else {
                equal.next = curr;
                equal = equal.next;
            }
            curr = curr.next;
        }
        
        smaller.next = null;
        greater.next = null;
        equal.next = null;
        
        ListNode sortedSmaller = sortList(smallerHead.next);
        ListNode sortedGreater = sortList(greaterHead.next);
        
        return concatenate(sortedSmaller, equalHead.next, sortedGreater);
    }
    
    private ListNode concatenate(ListNode smaller, ListNode equal, ListNode greater) {
        ListNode dummy = new ListNode(0);
        ListNode tail = dummy;
        
        tail.next = smaller;
        while (tail.next != null) tail = tail.next;
        
        tail.next = equal;
        while (tail.next != null) tail = tail.next;
        
        tail.next = greater;
        
        return dummy.next;
    }
}
```
**Độ phức tạp:**
- Time: Average $O(N \log N)$, Worst $O(N^2)$.
- Space: $O(\log N)$ trên memory stack đệ quy.

---

## 14. Find the Median of an Unsorted Array
**Đề bài chi tiết:** Cho một mảng các số nguyên chưa được sắp xếp, hãy tìm phần tử trung vị của mảng. Nếu độ dài mảng là chẵn, trung vị là trung bình cộng của hai phần tử ở giữa. Thời gian chạy yêu cầu trung bình là $O(N)$.
**Phân tích thuật toán:** Sử dụng thuật toán Quick Select để tìm phần tử lớn thứ `N / 2`. Đối với mảng có số lượng phần tử lẻ, phần tử ở vị trí `N / 2` chính là trung vị. Với mảng có độ dài chẵn, ta cần tìm hai phần tử ở vị trí `N / 2 - 1` và `N / 2`, sau đó tính trung bình cộng của chúng.
**Mã nguồn Java:**
```java
class Solution {
    public double findMedian(int[] nums) {
        int n = nums.length;
        if (n % 2 == 1) {
            return (double) quickSelect(nums, 0, n - 1, n / 2);
        } else {
            int leftMid = quickSelect(nums, 0, n - 1, n / 2 - 1);
            int rightMid = quickSelect(nums, 0, n - 1, n / 2);
            return (leftMid + rightMid) / 2.0;
        }
    }
    
    private int quickSelect(int[] nums, int left, int right, int k) {
        if (left == right) return nums[left];
        
        int pivotIndex = partition(nums, left, right);
        if (pivotIndex == k) {
            return nums[pivotIndex];
        } else if (pivotIndex < k) {
            return quickSelect(nums, pivotIndex + 1, right, k);
        } else {
            return quickSelect(nums, left, pivotIndex - 1, k);
        }
    }
    
    private int partition(int[] nums, int left, int right) {
        int randomIndex = left + (int)(Math.random() * (right - left + 1));
        swap(nums, randomIndex, right);
        
        int pivot = nums[right];
        int i = left;
        for (int j = left; j < right; j++) {
            if (nums[j] < pivot) {
                swap(nums, i, j);
                i++;
            }
        }
        swap(nums, i, right);
        return i;
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
    }
}
```
**Độ phức tạp:**
- Time: Average $O(N)$, Worst $O(N^2)$.
- Space: $O(\log N)$ đệ quy.

---

## 15. Relative Sort Array
**Đề bài chi tiết:** Cho hai mảng `arr1` và `arr2`, các phần tử của `arr2` là duy nhất và tất cả các phần tử của `arr2` đều nằm trong `arr1`. Sắp xếp các phần tử của `arr1` sao cho thứ tự tương đối của các phần tử trong `arr1` giống như trong `arr2`. Các phần tử không xuất hiện trong `arr2` phải được đặt ở cuối `arr1` theo thứ tự tăng dần.
**Phân tích thuật toán:** Ta có thể định nghĩa một bộ so sánh (Comparator) tùy chỉnh cho `arr1`, sau đó áp dụng Quick Sort (hoặc hàm `Arrays.sort()` có sẵn vốn dùng Timsort/Quick Sort). Comparator sẽ dựa trên vị trí của từng phần tử trong `arr2` thông qua HashMap. Nếu phần tử không có trong `arr2`, nó được ưu tiên thấp hơn và so sánh bằng giá trị thông thường.
**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int[] relativeSortArray(int[] arr1, int[] arr2) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < arr2.length; i++) {
            map.put(arr2[i], i);
        }
        
        Integer[] arr = new Integer[arr1.length];
        for (int i = 0; i < arr1.length; i++) {
            arr[i] = arr1[i];
        }
        
        Arrays.sort(arr, (a, b) -> {
            if (map.containsKey(a) && map.containsKey(b)) {
                return map.get(a) - map.get(b);
            } else if (map.containsKey(a)) {
                return -1; // a nằm trong arr2, b thì không -> a đứng trước
            } else if (map.containsKey(b)) {
                return 1;  // b nằm trong arr2, a thì không -> b đứng trước
            } else {
                return a - b; // Cả hai không nằm trong arr2 -> so sánh giá trị
            }
        });
        
        for (int i = 0; i < arr.length; i++) {
            arr1[i] = arr[i];
        }
        return arr1;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$ (thời gian sắp xếp).
- Space: $O(N)$ lưu Map và mảng đối tượng `arr`.

---

## 16. Sort Array by Parity
**Đề bài chi tiết:** Cho một mảng các số nguyên `nums`, hãy di chuyển tất cả các số chẵn lên đầu mảng, sau đó là tất cả các số lẻ. Bạn có thể trả về bất kỳ mảng nào thỏa mãn điều kiện này.
**Phân tích thuật toán:** Đây là một dạng bài áp dụng kỹ thuật phân vùng (Partitioning) trong Quick Sort. Ta dùng 2 con trỏ: một con trỏ duyệt mảng (`j`) và một con trỏ lưu vị trí chèn số chẵn (`i`). Khi duyệt mảng, nếu gặp số chẵn, ta hoán đổi nó với vị trí `i` rồi tăng `i`.
**Mã nguồn Java:**
```java
class Solution {
    public int[] sortArrayByParity(int[] nums) {
        int i = 0;
        for (int j = 0; j < nums.length; j++) {
            if (nums[j] % 2 == 0) {
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;
                i++;
            }
        }
        return nums;
    }
}
```
**Độ phức tạp:**
- Time: $O(N)$.
- Space: $O(1)$ In-place.

---

## 17. Move Zeroes
**Đề bài chi tiết:** Cho một mảng `nums`, hãy viết hàm di chuyển tất cả các số `0` về cuối mảng trong khi vẫn giữ nguyên thứ tự tương đối của các phần tử khác `0`. Lưu ý rằng bạn phải thao tác trực tiếp trên mảng (in-place) mà không được tạo thêm bản sao mảng.
**Phân tích thuật toán:** Kỹ thuật này hoàn toàn tương đồng với phân vùng Lomuto (Lomuto Partition). Ta coi các phần tử khác `0` là những phần tử "nhỏ hơn pivot" cần gom về bên trái. Ta dùng biến `i` để đánh dấu ranh giới của các phần tử khác `0`. Nếu phần tử tại `j` khác 0, ta đổi chỗ nó với `i` và tăng `i`.
**Mã nguồn Java:**
```java
class Solution {
    public void moveZeroes(int[] nums) {
        int i = 0; // Vị trí chèn phần tử khác 0
        for (int j = 0; j < nums.length; j++) {
            if (nums[j] != 0) {
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;
                i++;
            }
        }
    }
}
```
**Độ phức tạp:**
- Time: $O(N)$.
- Space: $O(1)$ In-place.

---

## 18. Sort Array By Parity II
**Đề bài chi tiết:** Cho mảng `nums` gồm một nửa số chẵn và một nửa số lẻ. Sắp xếp mảng sao cho `nums[i]` là số chẵn nếu `i` chẵn, và `nums[i]` là số lẻ nếu `i` lẻ.
**Phân tích thuật toán:** Đây có thể xem như một ứng dụng phân vùng kết hợp điều kiện. Ta dùng hai con trỏ, `even` bắt đầu tại `0` (chỉ số chẵn) và `odd` bắt đầu tại `1` (chỉ số lẻ). Ta duyệt con trỏ `even` để tìm số lẻ nằm sai chỗ. Nếu thấy `nums[even]` là số lẻ, ta sẽ duyệt con trỏ `odd` để tìm số chẵn nằm sai chỗ ở các vị trí lẻ và hoán đổi chúng.
**Mã nguồn Java:**
```java
class Solution {
    public int[] sortArrayByParityII(int[] nums) {
        int even = 0, odd = 1;
        int n = nums.length;
        
        while (even < n && odd < n) {
            while (even < n && nums[even] % 2 == 0) {
                even += 2;
            }
            while (odd < n && nums[odd] % 2 == 1) {
                odd += 2;
            }
            if (even < n && odd < n) {
                int temp = nums[even];
                nums[even] = nums[odd];
                nums[odd] = temp;
            }
        }
        return nums;
    }
}
```
**Độ phức tạp:**
- Time: $O(N)$ vì mỗi con trỏ đi qua độ dài mảng tối đa 1 lần.
- Space: $O(1)$ In-place.

---

## 19. Wiggle Sort
**Đề bài chi tiết:** Cho một mảng `nums` chưa sắp xếp, hãy sắp xếp lại mảng in-place sao cho `nums[0] <= nums[1] >= nums[2] <= nums[3]...`.
**Phân tích thuật toán:** Khác với Wiggle Sort II đòi hỏi khắt khe `<`, `>`, bài này cho phép `=`. Cách đơn giản nhất là dùng Quick Sort sắp xếp toàn mảng rồi hoán đổi các phần tử liền kề ở vị trí lẻ. Tuy nhiên, có thể đạt $O(N)$ bằng cách duyệt mảng 1 lần, so sánh và swap trực tiếp với phần tử kề cạnh nếu nó vi phạm tính chất. Nếu coi đây là bài tập luyện tư duy Sorting, ta minh hoạ cách sử dụng Sorting (Quick Sort) rồi đảo vị trí.
**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public void wiggleSort(int[] nums) {
        Arrays.sort(nums); // Inbuilt Quick Sort
        // Đổi vị trí các phần tử liền kề để tạo wiggle
        for (int i = 1; i < nums.length - 1; i += 2) {
            int temp = nums[i];
            nums[i] = nums[i + 1];
            nums[i + 1] = temp;
        }
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$ để sắp xếp.
- Space: $O(\log N)$ tùy implement sắp xếp.

---

## 20. Largest Number
**Đề bài chi tiết:** Cho một mảng các số nguyên không âm `nums`, hãy sắp xếp chúng sao cho chúng hợp thành số lớn nhất. Kết quả trả về là một chuỗi.
**Phân tích thuật toán:** Bản chất là một bài toán sắp xếp với bộ so sánh (Comparator) đặc biệt. Giả sử ta có hai số dạng chuỗi `a` và `b`, ta so sánh bằng cách ghép chúng lại thành `a + b` và `b + a`. Nếu `a + b > b + a` thì `a` phải đứng trước `b`. Ta dùng thuật toán Quick Sort (thông qua `Arrays.sort`) trên mảng chuỗi với bộ so sánh trên.
**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public String largestNumber(int[] nums) {
        String[] strs = new String[nums.length];
        for (int i = 0; i < nums.length; i++) {
            strs[i] = String.valueOf(nums[i]);
        }
        
        Arrays.sort(strs, (a, b) -> {
            String order1 = a + b;
            String order2 = b + a;
            // Sắp xếp giảm dần
            return order2.compareTo(order1);
        });
        
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
- Time: $O(N \log N \times K)$, trong đó $K$ là độ dài trung bình của chuỗi.
- Space: $O(N)$ lưu mảng chuỗi `strs`.

---

## 21. Third Maximum Number
**Đề bài chi tiết:** Cho một mảng số nguyên `nums`, trả về số lớn thứ ba phân biệt trong mảng. Nếu số lớn thứ ba không tồn tại, trả về số lớn nhất.
**Phân tích thuật toán:** Ta có thể sử dụng HashSet để lọc các phần tử trùng lặp, sau đó chuyển về mảng và dùng thuật toán Quick Select tìm phần tử lớn thứ 3. Nếu số lượng phần tử duy nhất nhỏ hơn 3, ta trả về phần tử lớn nhất.
**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int thirdMax(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) {
            set.add(num);
        }
        
        int[] unique = new int[set.size()];
        int idx = 0;
        for (int num : set) {
            unique[idx++] = num;
        }
        
        if (unique.length < 3) {
            int max = unique[0];
            for (int i = 1; i < unique.length; i++) {
                max = Math.max(max, unique[i]);
            }
            return max;
        }
        
        return quickSelect(unique, 0, unique.length - 1, unique.length - 3);
    }
    
    private int quickSelect(int[] nums, int left, int right, int k) {
        if (left == right) return nums[left];
        
        int pivotIndex = partition(nums, left, right);
        if (pivotIndex == k) {
            return nums[pivotIndex];
        } else if (pivotIndex < k) {
            return quickSelect(nums, pivotIndex + 1, right, k);
        } else {
            return quickSelect(nums, left, pivotIndex - 1, k);
        }
    }
    
    private int partition(int[] nums, int left, int right) {
        int randomIndex = left + (int)(Math.random() * (right - left + 1));
        swap(nums, randomIndex, right);
        
        int pivot = nums[right];
        int i = left;
        for (int j = left; j < right; j++) {
            if (nums[j] < pivot) {
                swap(nums, i, j);
                i++;
            }
        }
        swap(nums, i, right);
        return i;
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
    }
}
```
**Độ phức tạp:**
- Time: Average $O(N)$.
- Space: $O(N)$ lưu các phần tử duy nhất vào Set và mảng.

---

## 22. Kth Largest Element in a Stream
**Đề bài chi tiết:** Thiết kế một lớp để tìm phần tử lớn thứ `k` trong một luồng số (stream). Lớp cần có hàm khởi tạo nhận vào số nguyên `k` và mảng `nums` ban đầu, và phương thức `add(int val)` để thêm một giá trị mới vào luồng và trả về phần tử lớn thứ `k` hiện tại.
**Phân tích thuật toán:** Mặc dù Min-Heap (PriorityQueue) là cách chuẩn mực nhất cho dạng bài luồng dữ liệu (Stream), ta có thể mô phỏng giải pháp bằng cách sử dụng Quick Select trong một mảng động cho mỗi lệnh gọi `add` (điều này chỉ phù hợp nếu ta không gọi hàm `add` quá nhiều hoặc nếu bài toán cho phép). Cách tiếp cận này minh hoạ việc tìm phần tử lớn thứ `k` ở một mảng tĩnh tại từng thời điểm. (Thực tế nên dùng Heap, ở đây ta minh họa Quick Select để đa dạng hóa cách sử dụng).
**Mã nguồn Java:**
```java
import java.util.*;

class KthLargest {
    private int k;
    private List<Integer> list;

    public KthLargest(int k, int[] nums) {
        this.k = k;
        this.list = new ArrayList<>();
        for (int num : nums) {
            this.list.add(num);
        }
    }
    
    public int add(int val) {
        list.add(val);
        // Chuyển List thành Array để dùng Quick Select
        int[] arr = new int[list.size()];
        for (int i = 0; i < list.size(); i++) {
            arr[i] = list.get(i);
        }
        return quickSelect(arr, 0, arr.length - 1, arr.length - k);
    }
    
    private int quickSelect(int[] nums, int left, int right, int k) {
        if (left >= right) return nums[left];
        
        int pivotIndex = partition(nums, left, right);
        if (pivotIndex == k) {
            return nums[pivotIndex];
        } else if (pivotIndex < k) {
            return quickSelect(nums, pivotIndex + 1, right, k);
        } else {
            return quickSelect(nums, left, pivotIndex - 1, k);
        }
    }
    
    private int partition(int[] nums, int left, int right) {
        int randomIndex = left + (int)(Math.random() * (right - left + 1));
        swap(nums, randomIndex, right);
        
        int pivot = nums[right];
        int i = left;
        for (int j = left; j < right; j++) {
            if (nums[j] < pivot) {
                swap(nums, i, j);
                i++;
            }
        }
        swap(nums, i, right);
        return i;
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
    }
}
```
**Độ phức tạp:**
- Time: Khởi tạo $O(N)$, mỗi lần add gọi Quick Select tốn trung bình $O(N)$.
- Space: $O(N)$ lưu List và mảng động.

---

## 23. Array Partition I
**Đề bài chi tiết:** Cho một mảng nguyên `nums` có `2n` phần tử, hãy ghép chúng thành `n` cặp `(a1, b1), (a2, b2), ..., (an, bn)` sao cho tổng của `min(ai, bi)` đối với tất cả `i` là lớn nhất.
**Phân tích thuật toán:** Để tổng các giá trị nhỏ nhất của các cặp đạt cực đại, khoảng cách giữa 2 phần tử trong một cặp phải càng nhỏ càng tốt. Nói cách khác, ta cần sắp xếp mảng theo thứ tự tăng dần. Sau khi sắp xếp (có thể sử dụng Quick Sort), ta lấy các phần tử ở vị trí chẵn `(0, 2, 4...)` làm thành phần nhỏ nhất của mỗi cặp và cộng tổng chúng lại.
**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int arrayPairSum(int[] nums) {
        Arrays.sort(nums); // Quick Sort
        int sum = 0;
        for (int i = 0; i < nums.length; i += 2) {
            sum += nums[i];
        }
        return sum;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$ (thời gian sắp xếp).
- Space: $O(\log N)$ (chi phí trên stack của thuật toán sort).

---

## 24. Kth Smallest Number in Multiplication Table
**Đề bài chi tiết:** Bảng cửu chương có kích thước `m x n` là một bảng số nguyên với phần tử tại hàng `i` cột `j` là `i * j` (1-indexed). Cho các số nguyên `m`, `n` và `k`, trả về phần tử nhỏ thứ `k` trong bảng cửu chương này.
**Phân tích thuật toán:** Ta không thể tạo mảng vì kích thước quá lớn. Thay vì Quick Select truyền thống, bài toán này áp dụng tư tưởng đếm số lượng phần tử "phân vùng" nhỏ hơn một giá trị `mid` (Binary Search the answer). Đây là dạng toán đếm dựa trên phân vùng, có tư duy rất giống với cách chia phân vùng Pivot: nếu số lượng phần tử nhỏ hơn hoặc bằng `mid` đạt đủ `k`, ta giới hạn khoảng tìm kiếm xuống nửa dưới.
**Mã nguồn Java:**
```java
class Solution {
    public int findKthNumber(int m, int n, int k) {
        int left = 1, right = m * n;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (countLessOrEqual(mid, m, n) >= k) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }
    
    // Đếm số lượng phần tử trong bảng m x n nhỏ hơn hoặc bằng x
    private int countLessOrEqual(int x, int m, int n) {
        int count = 0;
        for (int i = 1; i <= m; i++) {
            count += Math.min(x / i, n);
        }
        return count;
    }
}
```
**Độ phức tạp:**
- Time: $O(M \log(M \times N))$
- Space: $O(1)$.

---

## 25. Reorganize String
**Đề bài chi tiết:** Cho chuỗi `s`, hãy sắp xếp lại các ký tự của chuỗi sao cho không có 2 ký tự liền kề nào giống nhau. Trả về bất kỳ chuỗi thoả mãn, nếu không thể, trả về chuỗi rỗng.
**Phân tích thuật toán:** Đếm tần suất các ký tự. Ta cần đặt các ký tự có tần suất xuất hiện cao nhất cách xa nhau ra trước. Sau khi gom tần suất, ta có thể sắp xếp các ký tự theo tần suất giảm dần, hoặc dùng Max-Heap (hoặc Quick Sort). Sau đó phân vùng kết quả bằng cách điền các ký tự tại các chỉ số chẵn trước, rồi mới tới chỉ số lẻ, ngăn cách các ký tự giống nhau.
**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public String reorganizeString(String s) {
        int[] counts = new int[26];
        for (char c : s.toCharArray()) {
            counts[c - 'a'] += 100;
        }
        for (int i = 0; i < 26; i++) {
            counts[i] += i; // Encode the index to uniquely identify character
        }
        
        Arrays.sort(counts); // Sort based on count
        
        char[] res = new char[s.length()];
        int t = 1;
        for (int i = 0; i < 26; i++) {
            int count = counts[i] / 100;
            char ch = (char)('a' + (counts[i] % 100));
            if (count > (s.length() + 1) / 2) return ""; // Không thể thỏa mãn
            for (int j = 0; j < count; j++) {
                if (t >= s.length()) t = 0;
                res[t] = ch;
                t += 2;
            }
        }
        
        return new String(res);
    }
}
```
**Độ phức tạp:**
- Time: $O(N + 26 \log 26) = O(N)$.
- Space: $O(N)$ tạo chuỗi kết quả và mảng tần suất cỡ 26.

---

## 26. Sort Colors II
**Đề bài chi tiết:** Cho một mảng `colors` chứa `N` đối tượng được tô bằng $K$ màu khác nhau (các màu được đại diện bởi các số nguyên từ $1$ đến $K$). Hãy phân loại chúng sao cho các đối tượng cùng màu đứng cạnh nhau và thứ tự các màu tăng dần. Yêu cầu In-place sorting.
**Phân tích thuật toán:** Sử dụng Rainbow Sort (một biến thể mở rộng của thuật toán phân vùng Quick Sort). Ta chọn màu trung vị của đoạn màu `[colorLeft, colorRight]`, ví dụ `colorMid`. Sau đó dùng 2 con trỏ gom các phần tử có màu $\le colorMid$ về bên trái và $> colorMid$ về bên phải (giống hệt Partition). Tiếp tục đệ quy trên hai mảng con với khoảng màu tương ứng.
**Mã nguồn Java:**
```java
class Solution {
    public void sortColors2(int[] colors, int k) {
        if (colors == null || colors.length == 0) return;
        rainbowSort(colors, 0, colors.length - 1, 1, k);
    }
    
    private void rainbowSort(int[] colors, int left, int right, int colorStart, int colorEnd) {
        if (colorStart == colorEnd) return;
        if (left >= right) return;
        
        int colorMid = colorStart + (colorEnd - colorStart) / 2;
        int i = left, j = right;
        
        while (i <= j) {
            while (i <= j && colors[i] <= colorMid) i++;
            while (i <= j && colors[j] > colorMid) j--;
            if (i <= j) {
                int temp = colors[i];
                colors[i] = colors[j];
                colors[j] = temp;
                i++;
                j--;
            }
        }
        
        rainbowSort(colors, left, j, colorStart, colorMid);
        rainbowSort(colors, i, right, colorMid + 1, colorEnd);
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log K)$ vì có $O(\log K)$ mức đệ quy, mỗi mức quét qua $O(N)$ phần tử.
- Space: $O(\log K)$ bộ nhớ stack.

---

## 27. Minimum Difference Between Highest and Lowest of K Scores
**Đề bài chi tiết:** Cho mảng điểm số `nums` và một số nguyên `k`. Hãy chọn `k` điểm từ mảng sao cho sự chênh lệch giữa điểm cao nhất và điểm thấp nhất trong số `k` điểm đó là nhỏ nhất. Trả về giá trị chênh lệch tối thiểu này.
**Phân tích thuật toán:** Để hiệu số giữa phần tử lớn nhất và nhỏ nhất của `k` phần tử được chọn là nhỏ nhất, các phần tử này phải nằm gần nhau nhất về giá trị. Do đó, ta sắp xếp toàn bộ mảng (sử dụng Quick Sort) và sau đó dùng cửa sổ trượt (Sliding Window) kích thước `k` quét qua mảng, tìm chênh lệch `nums[i + k - 1] - nums[i]` nhỏ nhất.
**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int minimumDifference(int[] nums, int k) {
        if (k == 1) return 0;
        
        Arrays.sort(nums); // Quick Sort
        
        int minDiff = Integer.MAX_VALUE;
        for (int i = 0; i <= nums.length - k; i++) {
            minDiff = Math.min(minDiff, nums[i + k - 1] - nums[i]);
        }
        
        return minDiff;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$ cho việc sắp xếp.
- Space: $O(\log N)$.

---

## 28. Non-overlapping Intervals
**Đề bài chi tiết:** Cho một tập các đoạn thời gian `intervals`, tìm số đoạn thời gian tối thiểu cần phải xóa để phần còn lại của các đoạn thời gian không bị chồng chéo lên nhau.
**Phân tích thuật toán:** Bài toán này giải theo hướng Tham lam (Greedy). Để có thể chứa nhiều Interval nhất (loại bỏ ít nhất), ta cần các Interval kết thúc càng sớm càng tốt để nhường chỗ cho các Interval phía sau. Do đó, ta sắp xếp mảng các khoảng thời gian theo thời điểm kết thúc (end time) tăng dần bằng Quick Sort (`Arrays.sort`). Sau đó, duyệt từ trái sang phải, nếu phát hiện chênh lệch (đè lên nhau), ta loại bỏ khoảng hiện tại, ngược lại cập nhật điểm kết thúc.
**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        if (intervals.length == 0) return 0;
        
        // Sắp xếp các đoạn theo thời điểm kết thúc
        Arrays.sort(intervals, (a, b) -> a[1] - b[1]);
        
        int count = 0;
        int end = intervals[0][1];
        
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] < end) {
                // Có sự chồng chéo, phải xóa khoảng này
                count++;
            } else {
                // Không chồng chéo, cập nhật thời điểm kết thúc
                end = intervals[i][1];
            }
        }
        
        return count;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N)$ (thời gian sắp xếp).
- Space: $O(\log N)$ (để sắp xếp).

---

## 29. Find K-th Smallest Pair Distance
**Đề bài chi tiết:** Cho một mảng số nguyên `nums` và một số nguyên `k`. Khoảng cách của một cặp số được định nghĩa là giá trị tuyệt đối của sự khác biệt giữa hai số đó. Trả về khoảng cách cặp nhỏ thứ `k` trong tất cả các cặp có thể có.
**Phân tích thuật toán:** Giống bài Kth Smallest Number in Multiplication Table. Việc tính khoảng cách tất cả các cặp rồi áp dụng Quick Select tốn $O(N^2)$ dẫn tới TLE. Ta cần dùng Binary Search kết hợp đếm số lượng cặp. Sắp xếp mảng (Quick Sort) giúp ta đếm số lượng cặp có khoảng cách nhỏ hơn hoặc bằng một khoảng cách giả định `mid` trong thời gian $O(N)$ bằng kỹ thuật 2 con trỏ (giống logic gom phần tử về 1 phân vùng).
**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums); // Quick Sort
        
        int n = nums.length;
        int left = 0;
        int right = nums[n - 1] - nums[0];
        
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
    
    private int countPairs(int[] nums, int target) {
        int count = 0;
        int left = 0;
        for (int right = 0; right < nums.length; right++) {
            while (nums[right] - nums[left] > target) {
                left++;
            }
            count += right - left;
        }
        return count;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \log N + N \log(Max-Min))$.
- Space: $O(\log N)$.

---

## 30. Best Meeting Point
**Đề bài chi tiết:** Có một mảng hai chiều nhị phân `grid`, 1 đại diện cho nhà và 0 đại diện cho khoảng trống. Hãy tìm khoảng cách di chuyển (Khoảng cách Manhattan) nhỏ nhất để mọi người gặp nhau tại một điểm duy nhất (điểm đó không nhất thiết phải là khoảng trống 0).
**Phân tích thuật toán:** Khoảng cách Manhattan tính bằng tổng khoảng cách X và tổng khoảng cách Y. Bài toán này phân rã thành tìm điểm tụ họp tối ưu trên trục X và trục Y độc lập. Điểm tối ưu để tổng khoảng cách tới một tập hợp điểm trên một trục là nhỏ nhất chính là giá trị trung vị (Median). Ta thu thập tất cả toạ độ X và Y của các ngôi nhà, áp dụng Quick Select để tìm trung vị (hoặc đơn giản là sắp xếp Quick Sort rồi lấy phần tử ở giữa), sau đó cộng dồn khoảng cách.
**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int minTotalDistance(int[][] grid) {
        List<Integer> rows = new ArrayList<>();
        List<Integer> cols = new ArrayList<>();
        
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[0].length; j++) {
                if (grid[i][j] == 1) {
                    rows.add(i);
                    cols.add(j);
                }
            }
        }
        
        // rows đã được lấy theo thứ tự tăng dần
        // cols cần được sắp xếp để tìm trung vị
        Collections.sort(cols); // Quick Sort / Merge Sort cho danh sách
        
        int rowMedian = rows.get(rows.size() / 2);
        int colMedian = cols.get(cols.size() / 2);
        
        return minDistance1D(rows, rowMedian) + minDistance1D(cols, colMedian);
    }
    
    private int minDistance1D(List<Integer> points, int origin) {
        int dist = 0;
        for (int point : points) {
            dist += Math.abs(point - origin);
        }
        return dist;
    }
}
```
**Độ phức tạp:**
- Time: $O(M \times N)$ để thu thập tọa độ, $O(K \log K)$ để sắp xếp tọa độ cột với $K$ là số nhà. (Sử dụng Quick Select có thể tối ưu thêm).
- Space: $O(K)$ để lưu danh sách tọa độ.
