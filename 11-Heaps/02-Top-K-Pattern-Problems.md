# 02 - Top K Elements Pattern Problems

Tập hợp 30 bài tập về mẫu Top K Elements. Các bài từ 1-10 có mã nguồn Java và giải thích chi tiết, 11-30 được tóm tắt.

## 1. Top K Frequent Elements
- **Đề bài chi tiết:** Cho một mảng số nguyên `nums` và một số nguyên `k`. Hãy trả về `k` phần tử xuất hiện nhiều nhất trong mảng.
- **Phân tích thuật toán:** Sử dụng HashMap để đếm tần suất xuất hiện của từng phần tử. Sau đó, dùng một Min-Heap có kích thước `k` để lưu trữ các phần tử dựa trên tần suất của chúng. Nếu kích thước Heap vượt quá `k`, ta loại bỏ phần tử có tần suất nhỏ nhất.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freqMap = new HashMap<>();
        for (int num : nums) {
            freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);
        }
        
        PriorityQueue<Integer> minHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare(freqMap.get(a), freqMap.get(b))
        );
        
        for (int key : freqMap.keySet()) {
            minHeap.add(key);
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }
        
        int[] res = new int[k];
        for (int i = 0; i < k; i++) {
            res[i] = minHeap.poll();
        }
        return res;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log K)$, trong đó $N$ là số lượng phần tử.
  - Space Complexity: $O(N)$ cho HashMap.

## 2. K Closest Points to Origin
- **Đề bài chi tiết:** Cho một mảng các điểm `points` trong mặt phẳng 2D, với `points[i] = [xi, yi]` và số nguyên `k`. Trả về `k` điểm gần gốc tọa độ `(0, 0)` nhất.
- **Phân tích thuật toán:** Khoảng cách Euclidean từ điểm `(x, y)` tới gốc tọa độ tính bằng $x^2 + y^2$. Dùng Max-Heap kích thước `k` để duy trì `k` điểm gần nhất. Nếu khoảng cách của điểm đang xét nhỏ hơn khoảng cách ở gốc Max-Heap, ta đưa nó vào.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int[][] kClosest(int[][] points, int k) {
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare(b[0]*b[0] + b[1]*b[1], a[0]*a[0] + a[1]*a[1])
        );
        
        for (int[] point : points) {
            maxHeap.add(point);
            if (maxHeap.size() > k) {
                maxHeap.poll();
            }
        }
        
        int[][] res = new int[k][2];
        int i = 0;
        while (!maxHeap.isEmpty()) {
            res[i++] = maxHeap.poll();
        }
        return res;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log K)$
  - Space Complexity: $O(K)$ cho Heap.

## 3. Top K Frequent Words
- **Đề bài chi tiết:** Cho một mảng các chuỗi `words` và số nguyên `k`. Trả về `k` chuỗi xuất hiện nhiều nhất. Sắp xếp kết quả theo tần suất giảm dần. Nếu hai từ có cùng tần suất, xếp theo thứ tự từ điển (lexicographical order) tăng dần.
- **Phân tích thuật toán:** Đếm tần suất dùng HashMap. Dùng Min-Heap size `k`. Lưu ý Custom Comparator: Nếu tần suất khác nhau, ưu tiên phần tử có tần suất nhỏ ở trên đỉnh (để loại đi); nếu tần suất bằng nhau, ưu tiên phần tử đứng SAU trong từ điển ở trên đỉnh (để bị loại khi size > k). Cuối cùng lấy kết quả từ Heap và `Collections.reverse`.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public List<String> topKFrequent(String[] words, int k) {
        Map<String, Integer> map = new HashMap<>();
        for (String w : words) map.put(w, map.getOrDefault(w, 0) + 1);
        
        PriorityQueue<String> minHeap = new PriorityQueue<>((a, b) -> {
            if (map.get(a).equals(map.get(b))) {
                return b.compareTo(a); // Tần suất bằng: ưu tiên từ điển NGƯỢC
            }
            return Integer.compare(map.get(a), map.get(b)); // Tần suất tăng dần
        });
        
        for (String w : map.keySet()) {
            minHeap.add(w);
            if (minHeap.size() > k) minHeap.poll();
        }
        
        List<String> res = new ArrayList<>();
        while (!minHeap.isEmpty()) res.add(minHeap.poll());
        Collections.reverse(res);
        return res;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log K)$
  - Space Complexity: $O(N)$

## 4. Sort Characters By Frequency
- **Đề bài chi tiết:** Cho chuỗi `s`, hãy sắp xếp lại chuỗi dựa trên tần suất xuất hiện của các ký tự theo thứ tự giảm dần.
- **Phân tích thuật toán:** Đếm tần suất mỗi ký tự. Sử dụng Max-Heap chứa các `Map.Entry` hoặc ký tự, xếp theo tần suất giảm dần. Do số lượng ký tự tối đa là giới hạn (256 ASCII), Max-Heap hoặc Bucket Sort đều hoạt động cực nhanh.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public String frequencySort(String s) {
        Map<Character, Integer> map = new HashMap<>();
        for (char c : s.toCharArray()) {
            map.put(c, map.getOrDefault(c, 0) + 1);
        }
        
        PriorityQueue<Character> maxHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare(map.get(b), map.get(a))
        );
        
        maxHeap.addAll(map.keySet());
        
        StringBuilder sb = new StringBuilder();
        while (!maxHeap.isEmpty()) {
            char current = maxHeap.poll();
            int freq = map.get(current);
            for (int i = 0; i < freq; i++) {
                sb.append(current);
            }
        }
        return sb.toString();
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log M)$ với $M$ là số lượng ký tự duy nhất (thường $M \le 256$).
  - Space Complexity: $O(M)$

## 5. Find K Pairs with Smallest Sums
- **Đề bài chi tiết:** Cho 2 mảng nguyên đã sắp xếp `nums1` và `nums2` cùng với số `k`. Tìm `k` cặp phần tử `(u, v)` (trong đó $u \in nums1$, $v \in nums2$) có tổng nhỏ nhất.
- **Phân tích thuật toán:** Dùng Min-Heap, khởi tạo bằng cách ghép phần tử đầu tiên của `nums2` với `k` phần tử đầu của `nums1` (vì mảng đã xếp). Ở mỗi bước, poll cặp nhỏ nhất ra, lấy phần tử tiếp theo trong `nums2` ghép vào `nums1` tương ứng và add lại vào Heap.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums1.length == 0 || nums2.length == 0 || k == 0) return res;
        
        PriorityQueue<int[]> minHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare(a[0] + a[1], b[0] + b[1])
        );
        
        for (int i = 0; i < Math.min(nums1.length, k); i++) {
            minHeap.add(new int[]{nums1[i], nums2[0], 0}); // [val1, val2, index2]
        }
        
        while (k-- > 0 && !minHeap.isEmpty()) {
            int[] curr = minHeap.poll();
            res.add(Arrays.asList(curr[0], curr[1]));
            
            if (curr[2] == nums2.length - 1) continue;
            minHeap.add(new int[]{curr[0], nums2[curr[2] + 1], curr[2] + 1});
        }
        return res;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(K \log K)$
  - Space Complexity: $O(K)$

## 6. Kth Largest Element in a Stream
- **Đề bài chi tiết:** Thiết kế một lớp để tìm phần tử lớn thứ `k` trong một stream dữ liệu số nguyên. Lớp `KthLargest` cần khởi tạo với số `k` và mảng `nums`. Phương thức `add(int val)` trả về phần tử lớn thứ `k`.
- **Phân tích thuật toán:** Duy trì một Min-Heap kích thước cố định là `k`. Khi thêm phần tử mới, add vào Heap. Nếu `size > k`, loại bỏ đỉnh. Đỉnh của Heap lúc này luôn là phần tử lớn thứ `k`.
- **Mã nguồn Java:**
```java
import java.util.*;

class KthLargest {
    private PriorityQueue<Integer> minHeap;
    private int k;

    public KthLargest(int k, int[] nums) {
        this.k = k;
        minHeap = new PriorityQueue<>();
        for (int num : nums) {
            add(num);
        }
    }
    
    public int add(int val) {
        minHeap.add(val);
        if (minHeap.size() > k) {
            minHeap.poll();
        }
        return minHeap.peek();
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(\log K)$ cho mỗi thao tác `add`.
  - Space Complexity: $O(K)$ cho Heap.

## 7. Least Number of Unique Integers after K Removals
- **Đề bài chi tiết:** Cho mảng `arr` và số `k`. Xóa đúng `k` phần tử sao cho số lượng phần tử duy nhất còn lại là ít nhất.
- **Phân tích thuật toán:** Để tối thiểu số lượng phần tử duy nhất, ta nên xóa các phần tử có tần suất xuất hiện ít nhất trước. Đếm tần suất bằng HashMap, đưa tần suất vào Min-Heap, loại bỏ dần cho đến khi số lượng `k` bị trừ hết.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int findLeastNumOfUniqueInts(int[] arr, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int a : arr) {
            map.put(a, map.getOrDefault(a, 0) + 1);
        }
        
        PriorityQueue<Integer> minHeap = new PriorityQueue<>(map.values());
        
        while (!minHeap.isEmpty()) {
            k -= minHeap.peek();
            if (k < 0) {
                return minHeap.size();
            }
            minHeap.poll();
        }
        return 0;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log N)$
  - Space Complexity: $O(N)$

## 8. Reorganize String
- **Đề bài chi tiết:** Cho chuỗi `S`, sắp xếp lại ký tự sao cho không có hai ký tự liền kề nào giống nhau. Nếu không thể, trả về chuỗi rỗng.
- **Phân tích thuật toán:** Đếm tần suất. Dùng Max-Heap để lưu ký tự có tần suất cao nhất. Ở mỗi bước, lấy ra 2 ký tự có tần suất cao nhất (để tránh lấy liên tiếp), ghép vào chuỗi kết quả, giảm tần suất và đưa ngược lại Heap.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public String reorganizeString(String s) {
        Map<Character, Integer> map = new HashMap<>();
        for (char c : s.toCharArray()) map.put(c, map.getOrDefault(c, 0) + 1);
        
        PriorityQueue<Character> maxHeap = new PriorityQueue<>((a, b) -> map.get(b) - map.get(a));
        maxHeap.addAll(map.keySet());
        
        StringBuilder sb = new StringBuilder();
        while (maxHeap.size() >= 2) {
            char c1 = maxHeap.poll();
            char c2 = maxHeap.poll();
            sb.append(c1).append(c2);
            
            map.put(c1, map.get(c1) - 1);
            map.put(c2, map.get(c2) - 1);
            
            if (map.get(c1) > 0) maxHeap.add(c1);
            if (map.get(c2) > 0) maxHeap.add(c2);
        }
        
        if (!maxHeap.isEmpty()) {
            char last = maxHeap.poll();
            if (map.get(last) > 1) return "";
            sb.append(last);
        }
        return sb.toString();
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log M)$ ($M \le 26$).
  - Space Complexity: $O(M)$

## 9. Reduce Array Size to The Half
- **Đề bài chi tiết:** Chọn một tập hợp các số nguyên và xóa tất cả sự xuất hiện của chúng trong mảng. Tìm kích thước tập hợp nhỏ nhất sao cho số phần tử còn lại trong mảng giảm đi ít nhất một nửa.
- **Phân tích thuật toán:** Tương tự bài trên, ta đếm tần suất bằng HashMap. Dùng Max-Heap (hoặc mảng đếm) chứa các tần suất. Rút liên tục từ Max-Heap cho đến khi tổng số phần tử bị loại $\ge N/2$.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int minSetSize(int[] arr) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int num : arr) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }
        
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        for (int val : map.values()) {
            maxHeap.add(val);
        }
        
        int removed = 0;
        int half = arr.length / 2;
        int steps = 0;
        
        while (removed < half && !maxHeap.isEmpty()) {
            removed += maxHeap.poll();
            steps++;
        }
        
        return steps;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log N)$
  - Space Complexity: $O(N)$

## 10. Split Array into Consecutive Subsequences
- **Đề bài chi tiết:** Cho mảng `nums` tăng dần, chia nó thành một hoặc nhiều dãy con liên tiếp sao cho mỗi dãy con có độ dài ít nhất là 3. Trả về true nếu có thể chia, ngược lại false.
- **Phân tích thuật toán:** Có thể dùng HashMap (tần suất + dãy kết thúc) hoặc sử dụng PriorityQueue cho mỗi giá trị kết thúc. Ở đây ta dùng 2 HashMap: một cái đếm tần suất dư (`countMap`), một cái chứa số lượng dãy con đang kết thúc tại số `x` (`tailMap`).
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public boolean isPossible(int[] nums) {
        Map<Integer, Integer> count = new HashMap<>();
        Map<Integer, Integer> tail = new HashMap<>();
        
        for (int n : nums) count.put(n, count.getOrDefault(n, 0) + 1);
        
        for (int n : nums) {
            if (count.get(n) == 0) continue;
            
            if (tail.getOrDefault(n - 1, 0) > 0) {
                tail.put(n - 1, tail.get(n - 1) - 1);
                tail.put(n, tail.getOrDefault(n, 0) + 1);
            } else if (count.getOrDefault(n + 1, 0) > 0 && count.getOrDefault(n + 2, 0) > 0) {
                count.put(n + 1, count.get(n + 1) - 1);
                count.put(n + 2, count.get(n + 2) - 1);
                tail.put(n + 2, tail.getOrDefault(n + 2, 0) + 1);
            } else {
                return false;
            }
            count.put(n, count.get(n) - 1);
        }
        return true;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N)$
  - Space Complexity: $O(N)$

---

## 11. Kth Smallest Element in a Sorted Matrix
- **Đề bài chi tiết:** Cho một ma trận `n x n` tên là `matrix` trong đó mỗi hàng và mỗi cột được sắp xếp theo thứ tự tăng dần, và một số nguyên `k`. Hãy tìm phần tử nhỏ thứ `k` trong ma trận. Cần lưu ý rằng đó là phần tử nhỏ thứ `k` trong thứ tự đã được sắp xếp, không phải là phần tử phân biệt thứ `k`.
- **Phân tích thuật toán:** Ta có thể sử dụng một Min-Heap để giải bài toán này. Bắt đầu bằng cách đưa phần tử đầu tiên của mỗi hàng (cùng với tọa độ hàng, cột) vào Min-Heap. Mỗi lần lấy phần tử nhỏ nhất ra khỏi Heap, ta đưa phần tử tiếp theo trong cùng hàng vào Heap (nếu có). Lặp lại thao tác này `k - 1` lần, phần tử trên đỉnh Heap lúc này chính là phần tử nhỏ thứ `k`.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        PriorityQueue<int[]> minHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare(a[0], b[0])
        );
        
        for (int r = 0; r < Math.min(n, k); r++) {
            minHeap.add(new int[]{matrix[r][0], r, 0});
        }
        
        int count = 0;
        int ans = 0;
        while (!minHeap.isEmpty()) {
            int[] curr = minHeap.poll();
            ans = curr[0];
            count++;
            
            if (count == k) break;
            
            int r = curr[1];
            int c = curr[2];
            if (c + 1 < n) {
                minHeap.add(new int[]{matrix[r][c + 1], r, c + 1});
            }
        }
        
        return ans;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(K \log(\min(N, K)))$
  - Space Complexity: $O(\min(N, K))$

## 12. Kth Largest Element in an Array
- **Đề bài chi tiết:** Cho mảng số nguyên `nums` và số nguyên `k`, hãy trả về phần tử lớn thứ `k` trong mảng. Cần giải quyết bài toán với độ phức tạp thời gian $O(N)$ trong trường hợp trung bình.
- **Phân tích thuật toán:** Một cách kinh điển áp dụng Heap là sử dụng Min-Heap có kích thước `k`. Ta duyệt qua từng phần tử của mảng, thêm phần tử vào Min-Heap. Nếu kích thước Min-Heap vượt quá `k`, ta loại bỏ phần tử nhỏ nhất (tức là loại bỏ phần tử trên đỉnh Heap). Cuối cùng, phần tử trên đỉnh Heap sẽ là phần tử lớn thứ `k`.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int findKthLargest(int[] nums, int k) {
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        
        for (int num : nums) {
            minHeap.add(num);
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }
        
        return minHeap.peek();
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log K)$ với cách dùng Heap.
  - Space Complexity: $O(K)$ cho kích thước của Heap.

## 13. Find K-th Smallest Pair Distance
- **Đề bài chi tiết:** Cho một mảng số nguyên `nums` và một số nguyên `k`. Khoảng cách của một cặp `(A, B)` được định nghĩa là giá trị tuyệt đối giữa `A` và `B`. Hãy trả về khoảng cách cặp nhỏ thứ `k` trong số tất cả các cặp có thể có.
- **Phân tích thuật toán:** Nếu dùng Max-Heap chứa các khoảng cách thì độ phức tạp thời gian sẽ là $O(N^2 \log K)$, sẽ bị Time Limit Exceeded (TLE) với $N$ lớn. Cách tối ưu là sử dụng Binary Search kết hợp với Sliding Window (Two Pointers) để đếm số cặp có khoảng cách $\le M$. Ta tìm kiếm nhị phân giá trị khoảng cách từ $0$ đến $max(nums) - min(nums)$.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums);
        int n = nums.length;
        int low = 0;
        int high = nums[n - 1] - nums[0];
        
        while (low < high) {
            int mid = low + (high - low) / 2;
            int count = 0;
            int left = 0;
            
            for (int right = 0; right < n; right++) {
                while (nums[right] - nums[left] > mid) {
                    left++;
                }
                count += right - left;
            }
            
            if (count >= k) {
                high = mid;
            } else {
                low = mid + 1;
            }
        }
        
        return low;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log N + N \log D)$, với $D$ là hiệu của phần tử lớn nhất và nhỏ nhất.
  - Space Complexity: $O(1)$ sau khi sắp xếp (nếu dùng in-place sort).

## 14. Maximum Performance of a Team
- **Đề bài chi tiết:** Cho $n$ kỹ sư với tốc độ `speed` và hiệu suất `efficiency`. Chọn ra tối đa `k` kỹ sư để tạo thành một nhóm sao cho hiệu suất làm việc (Performance) là lớn nhất. `Performance` = Tổng tốc độ nhân với hiệu suất thấp nhất trong nhóm. Trả về kết quả modulo $10^9 + 7$.
- **Phân tích thuật toán:** Nhóm các kỹ sư thành từng cặp `(speed, efficiency)` và sắp xếp giảm dần theo `efficiency`. Khi duyệt qua từng kỹ sư trong danh sách đã xếp, kỹ sư hiện tại luôn là người có hiệu suất thấp nhất trong nhóm. Dùng Min-Heap lưu `speed` của các kỹ sư đã chọn để duy trì nhóm có kích thước tối đa `k`, luôn loại trừ kỹ sư có `speed` nhỏ nhất nếu kích thước nhóm vượt `k`.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int maxPerformance(int n, int[] speed, int[] efficiency, int k) {
        int[][] engineers = new int[n][2];
        for (int i = 0; i < n; i++) {
            engineers[i] = new int[]{speed[i], efficiency[i]};
        }
        
        // Sắp xếp giảm dần theo efficiency
        Arrays.sort(engineers, (a, b) -> Integer.compare(b[1], a[1]));
        
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        long maxPerformance = 0;
        long speedSum = 0;
        
        for (int[] engineer : engineers) {
            minHeap.add(engineer[0]);
            speedSum += engineer[0];
            
            if (minHeap.size() > k) {
                speedSum -= minHeap.poll();
            }
            
            maxPerformance = Math.max(maxPerformance, speedSum * engineer[1]);
        }
        
        return (int) (maxPerformance % 1000000007);
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log N + N \log K)$
  - Space Complexity: $O(N + K)$

## 15. Task Scheduler
- **Đề bài chi tiết:** Cho một mảng các ký tự `tasks` biểu diễn các tác vụ cần CPU xử lý, mỗi chữ cái là một loại tác vụ khác nhau, và một số nguyên `n` là khoảng thời gian chờ (cooldown) bắt buộc giữa hai tác vụ cùng loại. Trả về số lượng đơn vị thời gian tối thiểu để hoàn thành mọi tác vụ.
- **Phân tích thuật toán:** Để tối ưu hóa, ta nên ưu tiên xử lý tác vụ có số lượng còn lại nhiều nhất. Sử dụng Max-Heap để lưu số lượng của từng loại tác vụ. Kết hợp với Queue để lưu các tác vụ đang trong thời gian chờ (dưới dạng cặp `[số lượng còn lại, thời điểm có thể thực hiện lại]`). Mỗi thời điểm, lấy tác vụ từ Max-Heap thực thi, nếu số lượng còn $> 1$ thì đẩy vào Queue. Khi tới thời điểm lấy được ra từ Queue, đẩy lại vào Max-Heap.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int leastInterval(char[] tasks, int n) {
        int[] count = new int[26];
        for (char task : tasks) count[task - 'A']++;
        
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        for (int c : count) {
            if (c > 0) maxHeap.add(c);
        }
        
        Queue<int[]> queue = new LinkedList<>();
        int time = 0;
        
        while (!maxHeap.isEmpty() || !queue.isEmpty()) {
            time++;
            
            if (!maxHeap.isEmpty()) {
                int curr = maxHeap.poll();
                curr--;
                if (curr > 0) {
                    queue.add(new int[]{curr, time + n});
                }
            }
            
            if (!queue.isEmpty() && queue.peek()[1] == time) {
                maxHeap.add(queue.poll()[0]);
            }
        }
        
        return time;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(T)$ với $T$ là tổng số tác vụ, thao tác Heap $O(1)$ do Heap có tối đa 26 phần tử.
  - Space Complexity: $O(1)$ do mảng và Heap kích thước tĩnh.

## 16. Rearrange String k Distance Apart
- **Đề bài chi tiết:** Cho một chuỗi `s` và một số nguyên `k`, hãy sắp xếp lại chuỗi sao cho các ký tự giống nhau phải cách nhau ít nhất `k` vị trí. Nếu không thể tạo được chuỗi thỏa mãn, trả về chuỗi rỗng.
- **Phân tích thuật toán:** Bài này tương tự như "Task Scheduler" nhưng cần xây dựng chuỗi kết quả. Dùng Max-Heap theo tần suất các ký tự. Dùng một Queue độ dài `k` (chứa các ký tự đang ở trạng thái cooldown) để trì hoãn việc đưa ký tự đó trở lại Max-Heap. Mỗi lần lấy từ Max-Heap 1 ký tự, nối vào kết quả, giảm tần suất và đưa vào Queue. Khi kích thước Queue đạt `k`, lấy phần tử đầu Queue ra, nếu tần suất $> 0$ thì cho lại vào Heap.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public String rearrangeString(String s, int k) {
        if (k <= 1) return s;
        
        Map<Character, Integer> map = new HashMap<>();
        for (char c : s.toCharArray()) map.put(c, map.getOrDefault(c, 0) + 1);
        
        PriorityQueue<Map.Entry<Character, Integer>> maxHeap = new PriorityQueue<>(
            (a, b) -> b.getValue().equals(a.getValue()) ? 
                      a.getKey().compareTo(b.getKey()) : b.getValue() - a.getValue()
        );
        maxHeap.addAll(map.entrySet());
        
        Queue<Map.Entry<Character, Integer>> queue = new LinkedList<>();
        StringBuilder res = new StringBuilder();
        
        while (!maxHeap.isEmpty()) {
            Map.Entry<Character, Integer> curr = maxHeap.poll();
            res.append(curr.getKey());
            curr.setValue(curr.getValue() - 1);
            queue.offer(curr);
            
            if (queue.size() == k) {
                Map.Entry<Character, Integer> entry = queue.poll();
                if (entry.getValue() > 0) {
                    maxHeap.add(entry);
                }
            }
        }
        
        return res.length() == s.length() ? res.toString() : "";
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log C)$ với $C \le 26$ là số lượng ký tự khác biệt.
  - Space Complexity: $O(C)$ cho HashMap, Queue và Heap.

## 17. Course Schedule III
- **Đề bài chi tiết:** Cho $n$ khóa học trực tuyến, mỗi khóa học có `duration` (thời lượng) và `lastDay` (ngày muộn nhất phải hoàn thành). Bắt đầu từ ngày 1, chỉ học 1 khóa tại một thời điểm. Trả về số lượng khóa học tối đa có thể tham gia.
- **Phân tích thuật toán:** Đầu tiên, sắp xếp các khóa học theo `lastDay` tăng dần. Dùng Max-Heap lưu trữ thời lượng `duration` của các khóa học đã chọn. Theo dõi `time` tổng. Nếu khóa học tiếp theo có thể học kịp (`time + duration <= lastDay`), thì thêm vào Heap và tăng `time`. Nếu không kịp nhưng `duration` của nó ngắn hơn khóa dài nhất đang có trong Max-Heap, ta thay khóa dài nhất bằng khóa này (giảm tổng `time`, tạo không gian cho các khóa sau).
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int scheduleCourse(int[][] courses) {
        Arrays.sort(courses, (a, b) -> Integer.compare(a[1], b[1]));
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        
        int time = 0;
        for (int[] c : courses) {
            time += c[0];
            maxHeap.add(c[0]);
            
            if (time > c[1]) {
                time -= maxHeap.poll();
            }
        }
        
        return maxHeap.size();
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log N)$ cho việc sắp xếp và thêm/xóa trong Heap.
  - Space Complexity: $O(N)$ lưu các độ dài khóa học trong Heap.

## 18. Swim in Rising Water
- **Đề bài chi tiết:** Cho lưới `N x N` mang giá trị độ cao `grid[i][j]`. Mỗi phút $t$, mực nước sẽ dâng lên mức $t$. Bạn chỉ có thể bơi giữa các ô kề cạnh nếu độ cao của cả 2 ô đều $\le t$. Tìm thời gian ít nhất để bơi từ `(0, 0)` đến `(N-1, N-1)`.
- **Phân tích thuật toán:** Sử dụng thuật toán Dijkstra để tìm đường đi có nỗ lực nhỏ nhất. Dùng Min-Heap (Priority Queue) bắt đầu từ `(0, 0)`, phần tử lưu `[độ cao lớn nhất trên đường đi, r, c]`. Tại mỗi bước, lấy ô có độ cao lớn nhất nhỏ nhất từ Heap, đánh dấu visited, mở rộng ra các hướng và đẩy vào Heap ô mới với giá trị độ cao là `Math.max(độ cao ô mới, giá trị hiện tại)`. Đến khi lấy ra `(N-1, N-1)`, ta có kết quả.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int swimInWater(int[][] grid) {
        int n = grid.length;
        PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> a[0] - b[0]);
        boolean[][] visited = new boolean[n][n];
        
        minHeap.add(new int[]{grid[0][0], 0, 0});
        visited[0][0] = true;
        
        int[][] dirs = {{1,0}, {-1,0}, {0,1}, {0,-1}};
        int res = 0;
        
        while (!minHeap.isEmpty()) {
            int[] curr = minHeap.poll();
            res = Math.max(res, curr[0]);
            int r = curr[1], c = curr[2];
            
            if (r == n - 1 && c == n - 1) return res;
            
            for (int[] dir : dirs) {
                int nr = r + dir[0];
                int nc = c + dir[1];
                if (nr >= 0 && nr < n && !visited[nr][nc]) {
                    visited[nr][nc] = true;
                    minHeap.add(new int[]{grid[nr][nc], nr, nc});
                }
            }
        }
        return -1;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N^2 \log(N^2))$
  - Space Complexity: $O(N^2)$ cho mảng visited và Heap.

## 19. Path With Minimum Effort
- **Đề bài chi tiết:** Cho lưới 2D chứa các độ cao. Bạn muốn đi từ góc trên cùng bên trái đến góc dưới cùng bên phải. "Effort" của một đường đi là sự chênh lệch tuyệt đối lớn nhất về độ cao giữa 2 ô kề cạnh dọc theo đường đi. Tìm nỗ lực nhỏ nhất.
- **Phân tích thuật toán:** Tương tự "Swim in Rising Water", ta dùng Dijkstra. Min-Heap theo dõi các ô đang xét với trọng số là nỗ lực cực đại từ điểm xuất phát đến ô đó. Khởi tạo mảng `dist` với `Integer.MAX_VALUE`. Tại mỗi ô, duyệt các hướng, tính `nextEffort = Math.max(currentEffort, Math.abs(heights[nx][ny] - heights[x][y]))`. Nếu nhỏ hơn `dist[nx][ny]`, cập nhật và đưa vào Heap.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int minimumEffortPath(int[][] heights) {
        int m = heights.length, n = heights[0].length;
        int[][] dist = new int[m][n];
        for (int[] row : dist) Arrays.fill(row, Integer.MAX_VALUE);
        
        PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> Integer.compare(a[0], b[0]));
        minHeap.add(new int[]{0, 0, 0}); // {effort, r, c}
        dist[0][0] = 0;
        
        int[][] dirs = {{1,0}, {-1,0}, {0,1}, {0,-1}};
        
        while (!minHeap.isEmpty()) {
            int[] curr = minHeap.poll();
            int eff = curr[0], r = curr[1], c = curr[2];
            
            if (r == m - 1 && c == n - 1) return eff;
            if (eff > dist[r][c]) continue;
            
            for (int[] dir : dirs) {
                int nr = r + dir[0], nc = c + dir[1];
                if (nr >= 0 && nr < m && nc >= 0 && nc < n) {
                    int nextEff = Math.max(eff, Math.abs(heights[nr][nc] - heights[r][c]));
                    if (nextEff < dist[nr][nc]) {
                        dist[nr][nc] = nextEff;
                        minHeap.add(new int[]{nextEff, nr, nc});
                    }
                }
            }
        }
        return 0;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(M \times N \log(M \times N))$
  - Space Complexity: $O(M \times N)$ cho mảng khoảng cách và Heap.

## 20. Design Twitter
- **Đề bài chi tiết:** Thiết kế hệ thống Twitter hỗ trợ các tác vụ: đăng tweet, theo dõi (follow), bỏ theo dõi (unfollow), và lấy 10 tweet gần đây nhất từ user và những người user đó follow (News Feed).
- **Phân tích thuật toán:** Mỗi user có một tập `followed` và một Linked List chứa các tweets. Để lấy 10 tweets mới nhất, bài toán trở thành "Merge K Sorted Lists". Ta đưa tweet mới nhất (đầu danh sách) của bản thân user và những người user đang follow vào Max-Heap (sắp xếp theo timestamp giảm dần). Lấy dần tweet ra, và đưa tiếp tweet cũ hơn của người đó vào Heap cho đến khi đủ 10 tweet.
- **Mã nguồn Java:**
```java
import java.util.*;

class Twitter {
    private static int timeStamp = 0;
    
    private class Tweet {
        int id;
        int time;
        Tweet next;
        public Tweet(int id) {
            this.id = id;
            this.time = timeStamp++;
            this.next = null;
        }
    }
    
    private class User {
        int id;
        Set<Integer> followed;
        Tweet tweetHead;
        
        public User(int id) {
            this.id = id;
            followed = new HashSet<>();
            follow(id); // Tự follow chính mình
            tweetHead = null;
        }
        
        public void follow(int id) { followed.add(id); }
        public void unfollow(int id) { if (id != this.id) followed.remove(id); }
        
        public void post(int id) {
            Tweet t = new Tweet(id);
            t.next = tweetHead;
            tweetHead = t;
        }
    }
    
    private Map<Integer, User> userMap;

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
        
        for (int u : users) {
            User user = userMap.get(u);
            if (user != null && user.tweetHead != null) {
                maxHeap.add(user.tweetHead);
            }
        }
        
        while (!maxHeap.isEmpty() && res.size() < 10) {
            Tweet t = maxHeap.poll();
            res.add(t.id);
            if (t.next != null) {
                maxHeap.add(t.next);
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
        if (userMap.containsKey(followerId)) {
            userMap.get(followerId).unfollow(followeeId);
        }
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: `postTweet`, `follow`, `unfollow` là $O(1)$. `getNewsFeed` là $O(K \log K)$ với $K$ là số người user follow. Lấy tối đa 10 phần tử nên lấy ra cũng xấp xỉ $O(1)$.
  - Space Complexity: $O(U + T)$ với $U$ là số users và $T$ là tổng số tweets.

---

## 21. Super Ugly Number
- **Đề bài chi tiết:** Một số super ugly là số nguyên dương mà tất cả các thừa số nguyên tố của nó nằm trong một mảng `primes` cho trước. Cho số nguyên `n` và mảng `primes`, trả về số super ugly thứ `n`.
- **Phân tích thuật toán:** Tương tự bài Ugly Number II. Có thể dùng Min-Heap để sinh các số từ nhỏ đến lớn. Khởi tạo Heap chứa số `1`. Mỗi lần lấy số nhỏ nhất ra khỏi Heap (nếu trùng thì bỏ qua), rồi nhân số đó với từng số nguyên tố trong `primes` và đưa lại vào Heap. Tuy nhiên, cách tối ưu hơn là dùng mảng các con trỏ (mỗi con trỏ tương ứng với một số nguyên tố trong `primes`) và có thể tối ưu hơn nữa khi dùng Min-Heap chứa các mảng `[giá_trị_hiện_tại, số_nguyên_tố, chỉ_số_con_trỏ]`.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int nthSuperUglyNumber(int n, int[] primes) {
        int[] ugly = new int[n];
        ugly[0] = 1;
        
        PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> Integer.compare(a[0], b[0]));
        for (int i = 0; i < primes.length; i++) {
            minHeap.add(new int[]{primes[i], i, 0});
        }
        
        for (int i = 1; i < n; i++) {
            ugly[i] = minHeap.peek()[0];
            
            while (minHeap.peek()[0] == ugly[i]) {
                int[] curr = minHeap.poll();
                int primeIdx = curr[1];
                int uglyIdx = curr[2] + 1;
                minHeap.add(new int[]{primes[primeIdx] * ugly[uglyIdx], primeIdx, uglyIdx});
            }
        }
        
        return ugly[n - 1];
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log K)$ với $K$ là độ dài mảng `primes`.
  - Space Complexity: $O(N + K)$

## 22. The Skyline Problem
- **Đề bài chi tiết:** Bầu trời của một thành phố được tạo bởi các tòa nhà hình chữ nhật. Cho vị trí trái, phải và chiều cao của từng tòa nhà, hãy tìm ra "đường viền" (skyline) tạo bởi các tòa nhà này. Skyline được biểu diễn bởi một danh sách các điểm "khúc quanh".
- **Phân tích thuật toán:** Có thể giải bằng Max-Heap hoặc TreeMap (tối ưu hơn cho xóa). Chuyển mỗi tòa nhà thành 2 sự kiện: cạnh trái `(L, -H)` và cạnh phải `(R, H)`. Sắp xếp các sự kiện theo tọa độ x (ưu tiên bắt đầu trước, cao trước, kết thúc sau). Duyệt qua từng sự kiện, nếu là cạnh trái, thêm độ cao vào Max-Heap (hoặc TreeMap). Nếu là cạnh phải, xóa độ cao đó khỏi Max-Heap. Tại mỗi điểm, chiều cao hiện tại là giá trị lớn nhất trong Max-Heap. Nếu chiều cao này thay đổi so với điểm trước đó, ta ghi nhận một điểm của đường viền.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public List<List<Integer>> getSkyline(int[][] buildings) {
        List<int[]> events = new ArrayList<>();
        for (int[] b : buildings) {
            events.add(new int[]{b[0], -b[2]}); // Điểm bắt đầu, chiều cao âm
            events.add(new int[]{b[1], b[2]});  // Điểm kết thúc, chiều cao dương
        }
        
        Collections.sort(events, (a, b) -> {
            if (a[0] != b[0]) return Integer.compare(a[0], b[0]);
            return Integer.compare(a[1], b[1]);
        });
        
        List<List<Integer>> res = new ArrayList<>();
        // TreeMap dùng làm Max-Heap nhưng hỗ trợ xóa O(log N)
        TreeMap<Integer, Integer> maxHeap = new TreeMap<>(Collections.reverseOrder());
        maxHeap.put(0, 1);
        int prevMaxHeight = 0;
        
        for (int[] e : events) {
            int x = e[0];
            int h = e[1];
            
            if (h < 0) {
                maxHeap.put(-h, maxHeap.getOrDefault(-h, 0) + 1);
            } else {
                int count = maxHeap.get(h);
                if (count == 1) {
                    maxHeap.remove(h);
                } else {
                    maxHeap.put(h, count - 1);
                }
            }
            
            int currentMaxHeight = maxHeap.firstKey();
            if (prevMaxHeight != currentMaxHeight) {
                res.add(Arrays.asList(x, currentMaxHeight));
                prevMaxHeight = currentMaxHeight;
            }
        }
        
        return res;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log N)$ với $N$ là số tòa nhà.
  - Space Complexity: $O(N)$

## 23. Minimum Cost to Hire K Workers
- **Đề bài chi tiết:** Có $n$ công nhân, mỗi công nhân có `quality` (chất lượng) và `wage` (mức lương tối thiểu mong muốn). Cần thuê đúng `k` công nhân để thành lập một nhóm sao cho chi phí là thấp nhất. Quy tắc là trong nhóm, mọi người phải được trả lương tỷ lệ thuận với chất lượng của họ, và mỗi công nhân phải nhận được ít nhất mức `wage` của họ.
- **Phân tích thuật toán:** Tỷ lệ $\text{ratio} = \text{wage} / \text{quality}$. Để thỏa mãn mọi người trong nhóm, tỷ lệ chung của nhóm phải là tỷ lệ lớn nhất trong số các thành viên được chọn. Vậy ta cần tối thiểu $\text{ratio\_nhóm} \times \text{tổng\_quality\_nhóm}$. Sắp xếp công nhân theo ratio tăng dần. Duyệt qua từng công nhân (coi người hiện tại là người có tỷ lệ lớn nhất trong nhóm), thêm `quality` của họ vào một Max-Heap. Nếu số lượng thành viên $> k$, loại bỏ người có `quality` lớn nhất khỏi Heap (để giảm tổng quality). Khi nhóm đủ $k$ người, tính chi phí và cập nhật kết quả nhỏ nhất.
- **Mã nguồn Java:**
```java
import java.util.*;

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
        double qualitySum = 0;
        
        for (double[] worker : workers) {
            double ratio = worker[0];
            double q = worker[1];
            
            qualitySum += q;
            maxHeap.add(q);
            
            if (maxHeap.size() > k) {
                qualitySum -= maxHeap.poll();
            }
            
            if (maxHeap.size() == k) {
                minCost = Math.min(minCost, qualitySum * ratio);
            }
        }
        
        return minCost;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log N + N \log K)$
  - Space Complexity: $O(N + K)$

## 24. IPO (Maximize Capital)
- **Đề bài chi tiết:** Cho `k` dự án, mỗi dự án có `profits[i]` và số vốn tối thiểu `capital[i]` cần thiết để bắt đầu. Bạn được cấp vốn ban đầu là `w`. Chỉ được làm từng dự án một. Hãy tìm cách chọn tối đa `k` dự án để tối đa hóa số vốn cuối cùng.
- **Phân tích thuật toán:** Sử dụng hai Heap. Min-Heap dùng để lưu các dự án và sắp xếp theo vốn yêu cầu (`capital`). Max-Heap dùng để lưu các lợi nhuận (`profit`) của các dự án mà ta ĐÃ ĐỦ vốn để thực hiện. Lặp $k$ lần: Lấy tất cả các dự án trong Min-Heap có `capital <= w` đưa lợi nhuận của chúng vào Max-Heap. Sau đó, chọn dự án trên đỉnh Max-Heap để thực hiện (dự án khả thi có lợi nhuận cao nhất), cập nhật $w = w + \text{profit}$.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int findMaximizedCapital(int k, int w, int[] profits, int[] capital) {
        int n = profits.length;
        // Min-Heap theo vốn
        PriorityQueue<int[]> minCapitalHeap = new PriorityQueue<>((a, b) -> Integer.compare(a[0], b[0]));
        // Max-Heap theo lợi nhuận
        PriorityQueue<Integer> maxProfitHeap = new PriorityQueue<>(Collections.reverseOrder());
        
        for (int i = 0; i < n; i++) {
            minCapitalHeap.add(new int[]{capital[i], profits[i]});
        }
        
        for (int i = 0; i < k; i++) {
            while (!minCapitalHeap.isEmpty() && minCapitalHeap.peek()[0] <= w) {
                maxProfitHeap.add(minCapitalHeap.poll()[1]);
            }
            
            if (maxProfitHeap.isEmpty()) {
                break;
            }
            
            w += maxProfitHeap.poll();
        }
        
        return w;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log N + K \log N)$
  - Space Complexity: $O(N)$

## 25. Sliding Window Median
- **Đề bài chi tiết:** Cho mảng `nums` và một cửa sổ trượt kích thước `k`. Tìm trung vị (median) của cửa sổ tại mỗi vị trí khi nó trượt từ trái sang phải của mảng.
- **Phân tích thuật toán:** Bài toán này tương tự "Find Median from Data Stream" nhưng có thêm thao tác xóa phần tử cũ. Ta dùng hai Heap: Max-Heap cho nửa nhỏ hơn và Min-Heap cho nửa lớn hơn. Tuy nhiên, việc xóa phần tử khỏi Heap bằng `remove(Object)` tốn $O(K)$. Để tối ưu (Lazy Deletion), ta dùng một HashMap để đếm số lần một phần tử cần bị xóa nhưng vẫn còn đang "mắc kẹt" trên đỉnh các Heap. Cân bằng hai Heap sau mỗi thao tác thêm và xóa lazy.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public double[] medianSlidingWindow(int[] nums, int k) {
        double[] res = new double[nums.length - k + 1];
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        Map<Integer, Integer> delayed = new HashMap<>();
        
        int i = 0;
        while (i < k) {
            maxHeap.add(nums[i]);
            i++;
        }
        for (int j = 0; j < k / 2; j++) minHeap.add(maxHeap.poll());
        
        int balance = 0;
        int idx = 0;
        
        while (true) {
            res[idx] = k % 2 == 1 ? maxHeap.peek() : ((double) maxHeap.peek() + minHeap.peek()) / 2.0;
            if (i >= nums.length) break;
            
            int outNum = nums[i - k];
            int inNum = nums[i];
            i++;
            
            balance = 0;
            // Xử lý phần tử ra
            delayed.put(outNum, delayed.getOrDefault(outNum, 0) + 1);
            if (!maxHeap.isEmpty() && outNum <= maxHeap.peek()) balance--;
            else balance++;
            
            // Xử lý phần tử vào
            if (!maxHeap.isEmpty() && inNum <= maxHeap.peek()) {
                maxHeap.add(inNum);
                balance++;
            } else {
                minHeap.add(inNum);
                balance--;
            }
            
            // Cân bằng
            if (balance < 0) maxHeap.add(minHeap.poll());
            else if (balance > 0) minHeap.add(maxHeap.poll());
            
            // Lazy deletion trên đỉnh maxHeap
            while (!maxHeap.isEmpty() && delayed.getOrDefault(maxHeap.peek(), 0) > 0) {
                delayed.put(maxHeap.peek(), delayed.get(maxHeap.peek()) - 1);
                maxHeap.poll();
            }
            // Lazy deletion trên đỉnh minHeap
            while (!minHeap.isEmpty() && delayed.getOrDefault(minHeap.peek(), 0) > 0) {
                delayed.put(minHeap.peek(), delayed.get(minHeap.peek()) - 1);
                minHeap.poll();
            }
            idx++;
        }
        return res;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log K)$
  - Space Complexity: $O(K)$

## 26. Kth Smallest Element in a BST
- **Đề bài chi tiết:** Cho gốc của một cây tìm kiếm nhị phân (BST) và số nguyên `k`. Hãy trả về giá trị nhỏ thứ `k` của tất cả các node trong cây (giá trị 1-indexed).
- **Phân tích thuật toán:** Do tính chất của BST, việc duyệt cây theo thứ tự Inorder (Trái - Gốc - Phải) sẽ trả về một chuỗi các giá trị tăng dần. Ta chỉ cần duyệt cây Inorder, đếm số lượng node đã duyệt qua. Khi bộ đếm bằng `k`, giá trị của node hiện tại chính là đáp án. Cách này tiết kiệm bộ nhớ và không cần sử dụng Heap hay mảng phụ nào khác.
- **Mã nguồn Java:**
```java
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int x) { val = x; }
}

class Solution {
    private int count = 0;
    private int result = -1;

    public int kthSmallest(TreeNode root, int k) {
        inorder(root, k);
        return result;
    }

    private void inorder(TreeNode node, int k) {
        if (node == null || count >= k) return;
        
        inorder(node.left, k);
        
        count++;
        if (count == k) {
            result = node.val;
            return;
        }
        
        inorder(node.right, k);
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(H + K)$, trong đó $H$ là chiều cao của cây.
  - Space Complexity: $O(H)$ cho call stack của đệ quy.

## 27. Seat Reservation Manager
- **Đề bài chi tiết:** Thiết kế một hệ thống quản lý đặt chỗ. Khởi tạo với $n$ ghế được đánh số từ $1$ đến $n$. Có hai phương thức: `reserve()` sẽ đặt chỗ và trả về số ghế trống có giá trị nhỏ nhất, `unreserve(seatNumber)` sẽ hủy đặt chỗ cho ghế có số `seatNumber`.
- **Phân tích thuật toán:** Yêu cầu lấy ghế trống có số nhỏ nhất gợi ý việc dùng Min-Heap. Ta khởi tạo một Min-Heap và thêm tất cả số từ 1 đến $n$ vào. Tuy nhiên, cách này tốn $O(N)$ thời gian và không gian ban đầu. Tối ưu hơn: dùng một biến `marker` bắt đầu từ 1. Khi `reserve()` được gọi, nếu Min-Heap có phần tử (ghế bị trả lại), ta lấy từ đó. Ngược lại, lấy `marker` và tăng `marker` lên 1. Khi `unreserve(seatNumber)`, ta chỉ cần đẩy số ghế đó vào Min-Heap.
- **Mã nguồn Java:**
```java
import java.util.*;

class SeatManager {
    private PriorityQueue<Integer> availableSeats;
    private int marker;

    public SeatManager(int n) {
        availableSeats = new PriorityQueue<>();
        marker = 1;
    }
    
    public int reserve() {
        if (!availableSeats.isEmpty()) {
            return availableSeats.poll();
        }
        return marker++;
    }
    
    public void unreserve(int seatNumber) {
        availableSeats.add(seatNumber);
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: `reserve()` $O(\log N)$ nếu lấy từ Heap, hoặc $O(1)$ nếu dùng marker. `unreserve()` $O(\log N)$.
  - Space Complexity: $O(N)$ trong trường hợp tất cả các ghế bị trả lại.

## 28. Process Tasks Using Servers
- **Đề bài chi tiết:** Có $n$ server và $m$ task. Mỗi server có `weight` và `index`. Bạn cần xử lý các task đến vào thời điểm $j$ ($0 \le j < m$). Mỗi task cần một khoảng thời gian xử lý. Việc gán task cho server phải ưu tiên server đang rảnh có `weight` nhỏ nhất, nếu bằng nhau thì chọn `index` nhỏ nhất.
- **Phân tích thuật toán:** Dùng 2 PriorityQueue. Một Min-Heap `freeServers` lưu server rảnh, sắp xếp theo `(weight, index)`. Một Min-Heap `busyServers` lưu server bận, sắp xếp theo `(thời_điểm_xong, weight, index)`. Duyệt qua các task từ 0 đến $m-1$. Tại mỗi giây, giải phóng các server bận đã làm xong task (đẩy từ `busy` sang `free`). Nếu không có server rảnh, ta tua thời gian đến thời điểm một server bận làm xong task sớm nhất. Sau đó gán task cho server rảnh phù hợp.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int[] assignTasks(int[] servers, int[] tasks) {
        PriorityQueue<int[]> free = new PriorityQueue<>((a, b) -> {
            if (a[0] != b[0]) return Integer.compare(a[0], b[0]);
            return Integer.compare(a[1], b[1]);
        });
        
        PriorityQueue<int[]> busy = new PriorityQueue<>((a, b) -> {
            if (a[2] != b[2]) return Integer.compare(a[2], b[2]);
            if (a[0] != b[0]) return Integer.compare(a[0], b[0]);
            return Integer.compare(a[1], b[1]);
        });
        
        for (int i = 0; i < servers.length; i++) {
            free.add(new int[]{servers[i], i, 0}); // {weight, index, 0}
        }
        
        int[] res = new int[tasks.length];
        
        for (int j = 0; j < tasks.length; j++) {
            while (!busy.isEmpty() && busy.peek()[2] <= j) {
                int[] s = busy.poll();
                free.add(new int[]{s[0], s[1], 0});
            }
            
            if (free.isEmpty()) {
                int[] s = busy.poll();
                res[j] = s[1];
                s[2] += tasks[j];
                busy.add(s);
            } else {
                int[] s = free.poll();
                res[j] = s[1];
                s[2] = j + tasks[j];
                busy.add(s);
            }
        }
        
        return res;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O((N + M) \log N)$
  - Space Complexity: $O(N)$ lưu trữ server trong các Heap.

## 29. Find Median from Data Stream
- **Đề bài chi tiết:** Thiết kế hệ thống nhận các số từ một data stream và trả về số trung vị tại thời điểm bất kỳ với $O(1)$ time complexity cho thao tác `findMedian`.
- **Phân tích thuật toán:** Dùng 2 Heap. Max-Heap `lower` lưu nửa dưới (các số nhỏ hơn), Min-Heap `higher` lưu nửa trên (các số lớn hơn). Cân bằng hai Heap sao cho kích thước của `lower` luôn bằng hoặc lớn hơn `higher` đúng 1 đơn vị. Khi thêm phần tử: đẩy vào `lower`, lấy số lớn nhất của `lower` đẩy sang `higher`. Nếu kích thước `higher` lớn hơn `lower`, lấy số nhỏ nhất từ `higher` đẩy về lại `lower`. Trung vị sẽ là đỉnh của `lower` (nếu tổng số lẻ) hoặc trung bình cộng 2 đỉnh (nếu tổng số chẵn).
- **Mã nguồn Java:**
```java
import java.util.*;

class MedianFinder {
    private PriorityQueue<Integer> lower;  // Max-Heap
    private PriorityQueue<Integer> higher; // Min-Heap

    public MedianFinder() {
        lower = new PriorityQueue<>(Collections.reverseOrder());
        higher = new PriorityQueue<>();
    }
    
    public void addNum(int num) {
        lower.add(num);
        higher.add(lower.poll());
        
        if (lower.size() < higher.size()) {
            lower.add(higher.poll());
        }
    }
    
    public double findMedian() {
        if (lower.size() > higher.size()) {
            return lower.peek();
        }
        return ((double) lower.peek() + higher.peek()) / 2.0;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(\log N)$ cho `addNum`, $O(1)$ cho `findMedian`.
  - Space Complexity: $O(N)$ lưu trữ các phần tử.

## 30. Furthest Building You Can Reach
- **Đề bài chi tiết:** Bạn có một mảng độ cao của các tòa nhà, `bricks` (gạch) và `ladders` (thang). Nếu di chuyển sang tòa nhà tiếp theo cao hơn, bạn có thể dùng số gạch bằng độ chênh lệch hoặc dùng 1 thang. Tìm tòa nhà xa nhất bạn có thể đến được (chỉ số 0-indexed).
- **Phân tích thuật toán:** Ý tưởng là dùng Min-Heap để "để dành" thang cho những khoảng cách lớn nhất. Khi gặp độ chênh lệch dương, ta mặc định đưa nó vào Min-Heap (tức là giả sử dùng thang). Nếu số lượng thang vượt mức cho phép `ladders` (kích thước Heap > `ladders`), ta rút khoảng cách nhỏ nhất ra khỏi Min-Heap (tức là chuyển từ dùng thang sang dùng gạch). Nếu không đủ gạch cho việc này, thì ta không thể đi tiếp được và điểm hiện tại chính là điểm xa nhất.
- **Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int furthestBuilding(int[] heights, int bricks, int ladders) {
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        
        for (int i = 0; i < heights.length - 1; i++) {
            int diff = heights[i + 1] - heights[i];
            
            if (diff > 0) {
                minHeap.add(diff);
                
                if (minHeap.size() > ladders) {
                    bricks -= minHeap.poll();
                }
                
                if (bricks < 0) {
                    return i;
                }
            }
        }
        
        return heights.length - 1;
    }
}
```
- **Độ phức tạp:**
  - Time Complexity: $O(N \log L)$ với $N$ là số lượng tòa nhà, $L$ là số lượng thang.
  - Space Complexity: $O(L)$ cho Heap.
