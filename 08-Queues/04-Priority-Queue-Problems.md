# Bài 4: Bài tập Hàng đợi ưu tiên (Priority Queue / Heap)

Danh sách 30 bài toán về Priority Queue từ cơ bản đến nâng cao. 10 bài đầu có phân tích và code mẫu bằng Java.

## 1. Kth Largest Element in an Array (Phần tử lớn thứ K trong mảng)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` chưa được sắp xếp và một số nguyên `k`. Trả về phần tử lớn thứ `k` trong mảng đó. Chú ý rằng đó là phần tử lớn thứ `k` trong thứ tự đã sắp xếp, chứ không phải phần tử phân biệt thứ `k`.

**Phân tích thuật toán:**
Để tìm phần tử lớn thứ K, cách tốt nhất bằng Priority Queue là duy trì một Min Heap có kích thước tối đa là K. Ta sẽ duyệt qua từng phần tử trong mảng, thêm phần tử đó vào Min Heap. Nếu số lượng phần tử trong Heap vượt quá K, ta loại bỏ (poll) phần tử ở gốc (là phần tử nhỏ nhất trong Heap). Cuối cùng, phần tử nằm ở đỉnh của Min Heap sẽ là phần tử lớn thứ K.

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;

public class Solution {
    public int findKthLargest(int[] nums, int k) {
        // Khởi tạo Min Heap
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        for (int num : nums) {
            minHeap.offer(num);
            // Giữ cho kích thước của heap luôn <= k
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }
        return minHeap.peek();
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N * log K) - Với N là số phần tử, ta thêm và xóa trong heap size K.
- Không gian (Space): O(K) - Dành cho Min Heap kích thước K.

---

## 2. Top K Frequent Elements (K phần tử xuất hiện nhiều nhất)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` và một số nguyên `k`, hãy trả về `k` phần tử xuất hiện nhiều nhất. Bạn có thể trả về đáp án theo bất kỳ thứ tự nào.

**Phân tích thuật toán:**
Dùng một HashMap để đếm số lần xuất hiện (tần suất) của mỗi phần tử. Sau đó, dùng một Min Heap để lưu trữ tối đa K phần tử có tần suất cao nhất. Trong Priority Queue, ta sẽ so sánh các phần tử dựa trên tần suất của chúng (lấy từ HashMap).

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        // Đếm tần suất
        Map<Integer, Integer> countMap = new HashMap<>();
        for (int num : nums) {
            countMap.put(num, countMap.getOrDefault(num, 0) + 1);
        }
        
        // Min Heap so sánh theo tần suất
        PriorityQueue<Integer> minHeap = new PriorityQueue<>(
            (n1, n2) -> Integer.compare(countMap.get(n1), countMap.get(n2))
        );
        
        for (int num : countMap.keySet()) {
            minHeap.offer(num);
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }
        
        // Trích xuất kết quả
        int[] result = new int[k];
        for (int i = k - 1; i >= 0; i--) {
            result[i] = minHeap.poll();
        }
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N * log K) - N là số phần tử của nums.
- Không gian (Space): O(N + K) - HashMap lưu tối đa N phần tử, Heap lưu K phần tử.

---

## 3. Merge k Sorted Lists (Trộn k danh sách liên kết đã sắp xếp)
**Đề bài chi tiết:** Cho một mảng của `k` danh sách liên kết, mỗi danh sách liên kết đã được sắp xếp tăng dần. Hãy trộn tất cả các danh sách này thành một danh sách liên kết sắp xếp và trả về.

**Phân tích thuật toán:**
Sử dụng một Min Heap chứa node đầu tiên của mỗi danh sách liên kết. Heap sẽ so sánh dựa trên giá trị (val) của ListNode. Mỗi lần ta trích xuất node nhỏ nhất từ Heap nối vào kết quả, và nếu node vừa rút có node tiếp theo (`node.next != null`), ta đẩy tiếp nó vào Heap.

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;

class ListNode {
    int val;
    ListNode next;
    ListNode(int val) { this.val = val; }
}

public class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) return null;
        
        // Min Heap
        PriorityQueue<ListNode> minHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare(a.val, b.val)
        );
        
        // Bỏ head của tất cả các list vào Heap
        for (ListNode node : lists) {
            if (node != null) {
                minHeap.offer(node);
            }
        }
        
        ListNode dummy = new ListNode(0);
        ListNode current = dummy;
        
        while (!minHeap.isEmpty()) {
            ListNode minNode = minHeap.poll();
            current.next = minNode;
            current = current.next;
            
            if (minNode.next != null) {
                minHeap.offer(minNode.next);
            }
        }
        
        return dummy.next;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N * log k) - N là tổng số nodes, k là số lượng danh sách.
- Không gian (Space): O(k) - Kích thước của heap lớn nhất bằng k.

---

## 4. Find Median from Data Stream (Tìm trung vị của luồng dữ liệu)
**Đề bài chi tiết:** Cài đặt lớp `MedianFinder` hỗ trợ thêm một số vào cấu trúc dữ liệu (`addNum`) và trả về trung vị của tất cả các số đã thêm từ trước tới nay (`findMedian`).

**Phân tích thuật toán:**
Dùng hai Heap:
- `maxHeap` lưu nửa nhỏ hơn của dãy số.
- `minHeap` lưu nửa lớn hơn của dãy số.
Ta luôn duy trì tính chất: phần tử lớn nhất của maxHeap <= phần tử nhỏ nhất của minHeap. Số lượng phần tử chênh lệch tối đa là 1 (maxHeap có thể nhiều hơn minHeap 1 phần tử nếu tổng là số lẻ).
Trung vị sẽ là đỉnh của maxHeap (lẻ) hoặc trung bình cộng 2 đỉnh của 2 heap (chẵn).

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;
import java.util.Collections;

class MedianFinder {
    private PriorityQueue<Integer> maxHeap; // Nửa bé
    private PriorityQueue<Integer> minHeap; // Nửa lớn

    public MedianFinder() {
        maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        minHeap = new PriorityQueue<>();
    }
    
    public void addNum(int num) {
        maxHeap.offer(num);
        minHeap.offer(maxHeap.poll());
        
        if (maxHeap.size() < minHeap.size()) {
            maxHeap.offer(minHeap.poll());
        }
    }
    
    public double findMedian() {
        if (maxHeap.size() > minHeap.size()) {
            return maxHeap.peek();
        }
        return (maxHeap.peek() + minHeap.peek()) / 2.0;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): `addNum`: O(log N), `findMedian`: O(1).
- Không gian (Space): O(N) để lưu các số.

---

## 5. K Closest Points to Origin (K điểm gần gốc tọa độ nhất)
**Đề bài chi tiết:** Cho một mảng các điểm `points` trên mặt phẳng 2D với `points[i] = [xi, yi]` và một số nguyên `k`. Trả về `k` điểm gần gốc tọa độ (0, 0) nhất. 

**Phân tích thuật toán:**
Khoảng cách Euclide được tính bằng $x^2 + y^2$. Ta dùng một Max Heap lưu tối đa K điểm, ưu tiên lưu điểm có khoảng cách lớn nhất ở trên cùng. Khi gặp điểm mới có khoảng cách nhỏ hơn gốc của Max Heap, ta sẽ loại gốc cũ và cho điểm mới vào.

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;

public class Solution {
    public int[][] kClosest(int[][] points, int k) {
        // Max Heap
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare((b[0]*b[0] + b[1]*b[1]), (a[0]*a[0] + a[1]*a[1]))
        );
        
        for (int[] point : points) {
            maxHeap.offer(point);
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

**Độ phức tạp:**
- Thời gian (Time): O(N * log K).
- Không gian (Space): O(K).

---

## 6. Task Scheduler (Lập lịch tác vụ)
**Đề bài chi tiết:** Cho mảng các ký tự đại diện cho các tác vụ của CPU và thời gian chờ `n`. Phải chờ tối thiểu `n` chu kỳ giữa 2 tác vụ giống nhau. Tính tổng số đơn vị thời gian ít nhất cần thiết để hoàn thành toàn bộ tác vụ.

**Phân tích thuật toán:**
Cần lập lịch sao cho các tác vụ xuất hiện nhiều nhất được giãn cách bằng các tác vụ khác. Ta đếm tần suất và đưa vào một Max Heap. Ở mỗi chu kỳ kích thước (n+1), ta cố gắng thực thi các tác vụ khác nhau bằng cách rút ra từ Heap, sau đó đưa ngược lại nếu chúng chưa hoàn tất.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public int leastInterval(char[] tasks, int n) {
        int[] frequencies = new int[26];
        for (char t : tasks) frequencies[t - 'A']++;
        
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        for (int f : frequencies) {
            if (f > 0) maxHeap.offer(f);
        }
        
        int time = 0;
        Queue<int[]> waitQueue = new LinkedList<>();
        
        while (!maxHeap.isEmpty() || !waitQueue.isEmpty()) {
            time++;
            if (!maxHeap.isEmpty()) {
                int currentFreq = maxHeap.poll();
                currentFreq--;
                if (currentFreq > 0) {
                    waitQueue.offer(new int[]{currentFreq, time + n});
                }
            }
            
            if (!waitQueue.isEmpty() && waitQueue.peek()[1] == time) {
                maxHeap.offer(waitQueue.poll()[0]);
            }
        }
        return time;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(T * log(26)) = O(T) với T là số lượng tác vụ, do heap giới hạn size 26 ký tự.
- Không gian (Space): O(1).

---

## 7. Sort Characters By Frequency (Sắp xếp ký tự theo tần suất)
**Đề bài chi tiết:** Cho một chuỗi `s`, sắp xếp nó theo thứ tự giảm dần dựa vào tần suất xuất hiện của các ký tự. 

**Phân tích thuật toán:**
Dùng một HashMap để đếm tần suất. Dùng Max Heap để sắp xếp các ký tự này. Cuối cùng rút ra và nối chúng vào kết quả bằng StringBuilder.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public String frequencySort(String s) {
        Map<Character, Integer> count = new HashMap<>();
        for (char c : s.toCharArray()) {
            count.put(c, count.getOrDefault(c, 0) + 1);
        }
        
        PriorityQueue<Character> maxHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare(count.get(b), count.get(a))
        );
        maxHeap.addAll(count.keySet());
        
        StringBuilder sb = new StringBuilder();
        while (!maxHeap.isEmpty()) {
            char current = maxHeap.poll();
            int freq = count.get(current);
            for (int i = 0; i < freq; i++) {
                sb.append(current);
            }
        }
        return sb.toString();
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N * log K) trong đó K là số lượng ký tự phân biệt (tối đa 256).
- Không gian (Space): O(K).

---

## 8. Reorganize String (Tổ chức lại chuỗi)
**Đề bài chi tiết:** Cho một chuỗi `s`, hãy sắp xếp lại các ký tự sao cho không có 2 ký tự nào giống nhau đứng liền kề. Trả về chuỗi rỗng nếu không thể thỏa mãn.

**Phân tích thuật toán:**
Đếm tần suất. Dùng Max Heap. Rút 2 ký tự xuất hiện nhiều nhất vào mỗi lần lặp, nối vào StringBuilder, giảm tần suất và đưa lại vào Heap. Nếu chỉ còn 1 ký tự mà tần suất > 1 thì sẽ trả về chuỗi rỗng (không hợp lệ).

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public String reorganizeString(String s) {
        Map<Character, Integer> count = new HashMap<>();
        for (char c : s.toCharArray()) count.put(c, count.getOrDefault(c, 0) + 1);
        
        PriorityQueue<Character> maxHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare(count.get(b), count.get(a))
        );
        maxHeap.addAll(count.keySet());
        
        StringBuilder sb = new StringBuilder();
        while (maxHeap.size() >= 2) {
            char first = maxHeap.poll();
            char second = maxHeap.poll();
            
            sb.append(first);
            sb.append(second);
            
            count.put(first, count.get(first) - 1);
            count.put(second, count.get(second) - 1);
            
            if (count.get(first) > 0) maxHeap.offer(first);
            if (count.get(second) > 0) maxHeap.offer(second);
        }
        
        if (!maxHeap.isEmpty()) {
            char last = maxHeap.poll();
            if (count.get(last) > 1) return "";
            sb.append(last);
        }
        
        return sb.toString();
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N * log K) với K là 26 ký tự chữ cái.
- Không gian (Space): O(K) = O(26) = O(1).

---

## 9. The Skyline Problem
**Đề bài chi tiết:** Cho đường viền của các tòa nhà (hình chữ nhật) chiếu trên bầu trời, tìm danh sách các điểm ngoặt (đường chân trời) của bóng các tòa nhà đó.

**Phân tích thuật toán:**
Đưa tất cả các sự kiện (bắt đầu và kết thúc một tòa nhà) vào danh sách và sắp xếp. Duyệt qua các sự kiện, dùng Max Heap lưu trữ độ cao của các tòa nhà đang xét. Nếu độ cao hiện tại của tòa nhà trên đỉnh Heap khác với độ cao trước đó, ta thêm điểm đó vào danh sách Skyline. (Cần cấu trúc cho phép remove với O(log N) như TreeMap, hoặc dùng Heap + lazy deletion). Ở đây dùng mảng lưu tọa độ, height âm cho bắt đầu.

**Mã nguồn Java (Dùng TreeMap thay vì PriorityQueue để xóa O(log N)):**
```java
import java.util.*;

public class Solution {
    public List<List<Integer>> getSkyline(int[][] buildings) {
        List<int[]> events = new ArrayList<>();
        for (int[] b : buildings) {
            events.add(new int[]{b[0], -b[2]}); // Bắt đầu tòa nhà chiều cao -h
            events.add(new int[]{b[1], b[2]});  // Kết thúc tòa nhà chiều cao h
        }
        
        events.sort((a, b) -> {
            if (a[0] != b[0]) return Integer.compare(a[0], b[0]);
            return Integer.compare(a[1], b[1]);
        });
        
        List<List<Integer>> result = new ArrayList<>();
        TreeMap<Integer, Integer> heightMap = new TreeMap<>(Collections.reverseOrder());
        heightMap.put(0, 1);
        int prevMaxHeight = 0;
        
        for (int[] event : events) {
            if (event[1] < 0) {
                heightMap.put(-event[1], heightMap.getOrDefault(-event[1], 0) + 1);
            } else {
                int count = heightMap.get(event[1]);
                if (count == 1) heightMap.remove(event[1]);
                else heightMap.put(event[1], count - 1);
            }
            
            int currMaxHeight = heightMap.firstKey();
            if (prevMaxHeight != currMaxHeight) {
                result.add(Arrays.asList(event[0], currMaxHeight));
                prevMaxHeight = currMaxHeight;
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N log N) cho việc sort và update TreeMap.
- Không gian (Space): O(N).

---

## 10. Minimum Cost to Hire K Workers (Chi phí tối thiểu để thuê K công nhân)
**Đề bài chi tiết:** Có N công nhân, mỗi người có chất lượng (quality) và lương mong muốn (wage). Trả lương theo tỷ lệ chất lượng và ít nhất bằng mức lương mong muốn. Tìm chi phí thấp nhất để thuê K công nhân.

**Phân tích thuật toán:**
Tính tỷ lệ lương/chất lượng cho từng công nhân và sắp xếp tăng dần. Nhóm công nhân theo tỷ lệ nhỏ thì rẻ hơn. Khi thuê theo tỷ lệ của người thứ i, mọi người trước đó đều thỏa mãn điều kiện lương tối thiểu. Dùng Max Heap để lưu chất lượng (quality) của K công nhân đang được xét, lấy K người có chất lượng thấp nhất để giảm tổng chi phí (Total Quality * Current Ratio).

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public double mincostToHireWorkers(int[] quality, int[] wage, int k) {
        int n = quality.length;
        double[][] workers = new double[n][2];
        for (int i = 0; i < n; i++) {
            workers[i] = new double[]{(double)wage[i] / quality[i], (double)quality[i]};
        }
        Arrays.sort(workers, (a, b) -> Double.compare(a[0], b[0]));
        
        PriorityQueue<Double> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        double minCost = Double.MAX_VALUE;
        double totalQuality = 0;
        
        for (double[] worker : workers) {
            maxHeap.offer(worker[1]);
            totalQuality += worker[1];
            
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

**Độ phức tạp:**
- Thời gian (Time): O(N log N) để sắp xếp, cộng O(N log K) cho heap = O(N log N).
- Không gian (Space): O(N + K).

---

## 11. Sliding Window Maximum (Giá trị lớn nhất trong cửa sổ trượt)
**Đề bài chi tiết:** Cho một mảng số nguyên `nums` và một số nguyên `k`. Có một cửa sổ trượt kích thước `k` di chuyển từ ngoài cùng bên trái của mảng sang ngoài cùng bên phải. Bạn chỉ có thể nhìn thấy `k` số trong cửa sổ. Mỗi lần cửa sổ trượt dịch sang phải một vị trí. Trả về mảng chứa các giá trị lớn nhất của cửa sổ trượt.

**Phân tích thuật toán:**
Sử dụng Max Heap để lưu các phần tử dưới dạng mảng gồm hai giá trị: `{giá trị, chỉ số}`. Mỗi khi trượt cửa sổ, thêm phần tử mới vào Max Heap. Đồng thời, kiểm tra phần tử ở đỉnh của Max Heap (phần tử lớn nhất). Nếu chỉ số của phần tử này nằm ngoài cửa sổ trượt (tức là `< i - k + 1`), ta liên tục loại bỏ nó (Lazy Deletion). Phần tử trên đỉnh còn lại chính là giá trị lớn nhất trong cửa sổ hiện tại.

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;

public class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        int[] result = new int[n - k + 1];
        // Max Heap lưu {giá_trị, chỉ_số}
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>(
            (a, b) -> a[0] != b[0] ? Integer.compare(b[0], a[0]) : Integer.compare(b[1], a[1])
        );

        for (int i = 0; i < n; i++) {
            maxHeap.offer(new int[]{nums[i], i});
            if (i >= k - 1) {
                // Xóa các phần tử đã trượt ra khỏi cửa sổ (Lazy Deletion)
                while (maxHeap.peek()[1] <= i - k) {
                    maxHeap.poll();
                }
                result[i - k + 1] = maxHeap.peek()[0];
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N log N) - Trong trường hợp xấu nhất, toàn bộ phần tử nằm trong heap. (Dùng Deque tối ưu có thể đạt O(N)).
- Không gian (Space): O(N) - Để lưu Max Heap.

---

## 12. Kth Smallest Element in a Sorted Matrix (Phần tử nhỏ thứ K trong ma trận đã sắp xếp)
**Đề bài chi tiết:** Cho một ma trận `matrix` kích thước `n x n` với các hàng và cột được sắp xếp theo thứ tự tăng dần. Tìm phần tử nhỏ thứ `k` trong ma trận. Cần tìm phần tử nhỏ thứ `k` trong thứ tự đã sắp xếp chung, chứ không phải phần tử phân biệt.

**Phân tích thuật toán:**
Dùng một Min Heap lưu mảng con chứa `{giá_trị, hàng, cột}`. Khởi tạo Heap bằng cách đẩy phần tử đầu tiên của mỗi hàng (hoặc đến hàng thứ `k` nếu `n > k`) vào Heap. Sau đó, lặp `k-1` lần rút phần tử nhỏ nhất từ Heap ra. Khi rút một phần tử, nếu cùng hàng đó còn phần tử tiếp theo (cột + 1), ta đẩy phần tử đó vào Heap. Cuối cùng đỉnh Heap sẽ là kết quả.

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;

public class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        // Min Heap lưu {giá_trị, hàng, cột}
        PriorityQueue<int[]> minHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare(a[0], b[0])
        );
        
        for (int i = 0; i < Math.min(n, k); i++) {
            minHeap.offer(new int[]{matrix[i][0], i, 0});
        }
        
        while (k > 1) {
            int[] current = minHeap.poll();
            int row = current[1];
            int col = current[2];
            
            if (col + 1 < n) {
                minHeap.offer(new int[]{matrix[row][col + 1], row, col + 1});
            }
            k--;
        }
        
        return minHeap.peek()[0];
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(K log(min(N, K))) - Khởi tạo O(min(N, K)), mỗi thao tác rút và đẩy tốn O(log(min(N, K))).
- Không gian (Space): O(min(N, K)) - Số lượng phần tử tối đa trong Min Heap.

---

## 13. Find K Pairs with Smallest Sums (Tìm K cặp có tổng nhỏ nhất)
**Đề bài chi tiết:** Cho 2 mảng số nguyên `nums1` và `nums2` đã sắp xếp tăng dần và một số nguyên `k`. Tìm `k` cặp có tổng nhỏ nhất `(u, v)`, trong đó `u` thuộc `nums1` và `v` thuộc `nums2`.

**Phân tích thuật toán:**
Sử dụng Min Heap lưu tuple `{tổng, i, j}`, trong đó `i` là chỉ số của `nums1` và `j` là chỉ số của `nums2`. Bắt đầu bằng cách đẩy các cặp gồm mọi phần tử của `nums1` ghép với phần tử đầu tiên của `nums2` vào Heap. Rút ra phần tử nhỏ nhất, sau đó đẩy tiếp cặp mới bao gồm phần tử hiện tại của `nums1` và phần tử tiếp theo của `nums2` `{nums1[i], nums2[j+1]}` vào Heap. Lặp lại quá trình này `k` lần.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> result = new ArrayList<>();
        if (nums1.length == 0 || nums2.length == 0 || k == 0) return result;
        
        // Min Heap lưu {nums1[i] + nums2[j], i, j}
        PriorityQueue<int[]> minHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare(a[0], b[0])
        );
        
        for (int i = 0; i < Math.min(nums1.length, k); i++) {
            minHeap.offer(new int[]{nums1[i] + nums2[0], i, 0});
        }
        
        while (k > 0 && !minHeap.isEmpty()) {
            int[] current = minHeap.poll();
            int i = current[1];
            int j = current[2];
            
            result.add(Arrays.asList(nums1[i], nums2[j]));
            k--;
            
            if (j + 1 < nums2.length) {
                minHeap.offer(new int[]{nums1[i] + nums2[j + 1], i, j + 1});
            }
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(K log(min(N, K))) - N là độ dài nums1.
- Không gian (Space): O(min(N, K)).

---

## 14. Trapping Rain Water II (Giữ nước mưa 2D)
**Đề bài chi tiết:** Cho một ma trận 2D kích thước `m x n` biểu diễn độ cao của bản đồ. Tính tổng thể tích nước có thể giữ được sau khi trời mưa.

**Phân tích thuật toán:**
Phần nước ở ô hiện tại phụ thuộc vào biên độ thấp nhất bao quanh nó. Đẩy tất cả các ô trên đường viền của ma trận vào một Min Heap và đánh dấu chúng đã xét. Heap sẽ sắp xếp các ô theo độ cao tăng dần. Tại mỗi bước, rút ô có độ cao thấp nhất từ Heap ra. Sau đó, xét 4 ô kề cạnh nó chưa được thăm. Nước đọng lại trên các ô này (nếu có) sẽ bằng `độ cao hiện tại - độ cao ô kề`. Cập nhật độ cao mới bằng `max(độ cao hiện tại, độ cao ô kề)` và đẩy ô kề vào Heap.

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;

public class Solution {
    public int trapRainWater(int[][] heightMap) {
        if (heightMap == null || heightMap.length == 0 || heightMap[0].length == 0) return 0;
        
        int m = heightMap.length;
        int n = heightMap[0].length;
        boolean[][] visited = new boolean[m][n];
        
        // Min Heap lưu {độ_cao, hàng, cột}
        PriorityQueue<int[]> minHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare(a[0], b[0])
        );
        
        // Đẩy viền bản đồ vào Heap
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (i == 0 || i == m - 1 || j == 0 || j == n - 1) {
                    minHeap.offer(new int[]{heightMap[i][j], i, j});
                    visited[i][j] = true;
                }
            }
        }
        
        int water = 0;
        int[][] dirs = {{0,1}, {0,-1}, {1,0}, {-1,0}};
        
        while (!minHeap.isEmpty()) {
            int[] cell = minHeap.poll();
            int h = cell[0];
            int r = cell[1];
            int c = cell[2];
            
            for (int[] dir : dirs) {
                int nr = r + dir[0];
                int nc = c + dir[1];
                
                if (nr >= 0 && nr < m && nc >= 0 && nc < n && !visited[nr][nc]) {
                    visited[nr][nc] = true;
                    water += Math.max(0, h - heightMap[nr][nc]);
                    minHeap.offer(new int[]{Math.max(h, heightMap[nr][nc]), nr, nc});
                }
            }
        }
        return water;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(M*N log(M*N)).
- Không gian (Space): O(M*N).

---

## 15. Ugly Number II (Số xấu II)
**Đề bài chi tiết:** Một số Ugly là một số nguyên dương mà các thừa số nguyên tố của nó chỉ chứa 2, 3 và 5. Hãy tìm số Ugly thứ `n`.

**Phân tích thuật toán:**
Bắt đầu với Min Heap chứa số 1. Rút phần tử nhỏ nhất ra, nhân nó với 2, 3 và 5, rồi đẩy các tích vào Min Heap. Sử dụng một `HashSet` để tránh trùng lặp. Lặp lại việc rút khỏi Heap n lần. Lần thứ n lấy ra sẽ là số Ugly thứ n. (Cách tối ưu hơn dùng Dynamic Programming O(N), nhưng ở đây hướng đến áp dụng Heap).

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public int nthUglyNumber(int n) {
        PriorityQueue<Long> minHeap = new PriorityQueue<>();
        Set<Long> seen = new HashSet<>();
        
        minHeap.offer(1L);
        seen.add(1L);
        
        int[] primes = {2, 3, 5};
        long current = 1;
        
        for (int i = 0; i < n; i++) {
            current = minHeap.poll();
            for (int prime : primes) {
                long nextUgly = current * prime;
                if (!seen.contains(nextUgly)) {
                    seen.add(nextUgly);
                    minHeap.offer(nextUgly);
                }
            }
        }
        
        return (int) current;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N log N).
- Không gian (Space): O(N).

---

## 16. Super Ugly Number (Số siêu xấu)
**Đề bài chi tiết:** Cho số nguyên `n` và một mảng số nguyên tố `primes` sắp xếp tăng dần. Hãy tìm số Super Ugly thứ `n`. Một số được coi là Super Ugly nếu tất cả các thừa số nguyên tố của nó nằm trong mảng `primes`.

**Phân tích thuật toán:**
Phương pháp hoàn toàn tương tự **Ugly Number II**. Dùng Min Heap kết hợp Set, và nhân giá trị rút được từ Heap với mọi số nguyên tố trong `primes` để tiếp tục tạo số mới. 

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public int nthSuperUglyNumber(int n, int[] primes) {
        PriorityQueue<Long> minHeap = new PriorityQueue<>();
        Set<Long> seen = new HashSet<>();
        
        minHeap.offer(1L);
        seen.add(1L);
        
        long current = 1;
        
        for (int i = 0; i < n; i++) {
            current = minHeap.poll();
            for (int prime : primes) {
                long nextUgly = current * prime;
                if (!seen.contains(nextUgly)) {
                    seen.add(nextUgly);
                    minHeap.offer(nextUgly);
                }
            }
        }
        
        return (int) current;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N * K log(N * K)) với K là chiều dài mảng primes.
- Không gian (Space): O(N * K).

---

## 17. Cheapest Flights Within K Stops (Chuyến bay rẻ nhất với K điểm dừng)
**Đề bài chi tiết:** Cho `n` thành phố và mảng `flights` gồm các chuyến bay được biểu diễn dưới dạng `[u, v, price]` (từ thành phố `u` đến `v` với giá `price`). Cho thành phố xuất phát `src`, đích `dst` và tối đa `k` điểm dừng. Tìm chi phí thấp nhất để đi từ `src` đến `dst`. Nếu không có đường đi, trả về -1.

**Phân tích thuật toán:**
Sử dụng thuật toán Dijkstra nhưng thay đổi để quản lý số lượng điểm dừng (stops). Priority Queue chứa một mảng `{chi_phí_hiện_tại, đỉnh_hiện_tại, số_lần_dừng_đã_dùng}`. Trong mỗi bước, nếu ta đến `dst`, giá hiện tại là giá rẻ nhất. Ngược lại, nếu chưa vượt quá số lượng điểm dừng k cho phép, ta duyệt các láng giềng và cập nhật đường đi.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        Map<Integer, List<int[]>> adj = new HashMap<>();
        for (int[] flight : flights) {
            adj.putIfAbsent(flight[0], new ArrayList<>());
            adj.get(flight[0]).add(new int[]{flight[1], flight[2]});
        }
        
        // Min Heap lưu {cost, node, stops}
        PriorityQueue<int[]> minHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare(a[0], b[0])
        );
        minHeap.offer(new int[]{0, src, 0});
        
        // Mảng lưu chi phí rẻ nhất tương ứng với số điểm dừng stops
        int[] minStops = new int[n];
        Arrays.fill(minStops, Integer.MAX_VALUE);
        
        while (!minHeap.isEmpty()) {
            int[] curr = minHeap.poll();
            int cost = curr[0];
            int node = curr[1];
            int stops = curr[2];
            
            if (node == dst) return cost;
            
            // Nếu đã qua số lượng điểm dừng hoặc con đường này có nhiều điểm dừng hơn lịch sử từng đi
            if (stops > k || stops >= minStops[node]) continue;
            minStops[node] = stops;
            
            if (adj.containsKey(node)) {
                for (int[] next : adj.get(node)) {
                    minHeap.offer(new int[]{cost + next[1], next[0], stops + 1});
                }
            }
        }
        
        return -1;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(E + V log V) với E là số chuyến bay, V là số thành phố.
- Không gian (Space): O(E + V).

---

## 18. Network Delay Time (Thời gian trễ của mạng)
**Đề bài chi tiết:** Có `n` nút từ 1 đến `n` của một mạng. Bạn có mảng `times` với `times[i] = [u, v, w]` (tín hiệu đi từ `u` đến `v` mất thời gian `w`). Ta gửi một tín hiệu từ nút `k`. Tính thời gian tối thiểu để tất cả các nút nhận được tín hiệu. Nếu có nút không nhận được, trả về -1.

**Phân tích thuật toán:**
Dùng thuật toán Dijkstra chuẩn bằng Min Heap lưu `{thời_gian_đến, đỉnh}`. Vì ta muốn tín hiệu truyền đi nhỏ nhất, ta lấy nút trong Min Heap, lan truyền cho các láng giềng. Sau khi truyền đến mọi nút có thể, ta lấy khoảng cách xa nhất đã tìm thấy (vì các nút này chạy song song).

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public int networkDelayTime(int[][] times, int n, int k) {
        Map<Integer, List<int[]>> adj = new HashMap<>();
        for (int[] time : times) {
            adj.putIfAbsent(time[0], new ArrayList<>());
            adj.get(time[0]).add(new int[]{time[1], time[2]});
        }
        
        // Min Heap lưu {thời_gian, đỉnh}
        PriorityQueue<int[]> minHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare(a[0], b[0])
        );
        minHeap.offer(new int[]{0, k});
        
        Map<Integer, Integer> dist = new HashMap<>();
        
        while (!minHeap.isEmpty()) {
            int[] curr = minHeap.poll();
            int time = curr[0];
            int node = curr[1];
            
            if (dist.containsKey(node)) continue;
            dist.put(node, time);
            
            if (adj.containsKey(node)) {
                for (int[] next : adj.get(node)) {
                    if (!dist.containsKey(next[0])) {
                        minHeap.offer(new int[]{time + next[1], next[0]});
                    }
                }
            }
        }
        
        if (dist.size() != n) return -1;
        
        int maxTime = 0;
        for (int time : dist.values()) {
            maxTime = Math.max(maxTime, time);
        }
        return maxTime;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(E log V)
- Không gian (Space): O(E + V)

---

## 19. Path With Maximum Minimum Value (Đường đi có giá trị nhỏ nhất lớn nhất)
**Đề bài chi tiết:** Cho ma trận `grid` biểu diễn bản đồ có giá trị điểm. Hãy tìm đường đi từ ô trái trên (0,0) xuống phải dưới (m-1, n-1) sao cho "điểm thấp nhất trên con đường đó" là lớn nhất có thể.

**Phân tích thuật toán:**
Dùng Max Heap kết hợp thuật toán Dijkstra mở rộng đường đi. Bắt đầu với Max Heap lưu ô `(0,0)`, giá trị lớn nhất luôn được ưu tiên mở rộng. Mỗi lần lấy 1 ô, giá trị đường đi kết thúc ở ô đó được quyết định bằng `min(giá_trị_điểm_đường_cũ, giá_trị_ô_hiện_tại)`. Sau đó đánh dấu ô này và tiếp tục đẩy láng giềng vào Heap. Đích đến cuối cùng lấy ra khỏi Heap sẽ cho giá trị điểm đúng yêu cầu.

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;

public class Solution {
    public int maximumMinimumPath(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        
        // Max Heap lưu {min_giá_trị_trên_đường, hàng, cột}
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare(b[0], a[0])
        );
        maxHeap.offer(new int[]{grid[0][0], 0, 0});
        
        boolean[][] visited = new boolean[m][n];
        visited[0][0] = true;
        
        int[][] dirs = {{0,1}, {0,-1}, {1,0}, {-1,0}};
        
        while (!maxHeap.isEmpty()) {
            int[] cell = maxHeap.poll();
            int val = cell[0];
            int r = cell[1];
            int c = cell[2];
            
            if (r == m - 1 && c == n - 1) return val;
            
            for (int[] dir : dirs) {
                int nr = r + dir[0];
                int nc = c + dir[1];
                
                if (nr >= 0 && nr < m && nc >= 0 && nc < n && !visited[nr][nc]) {
                    visited[nr][nc] = true;
                    // Lấy min giữa kết quả cũ và giá trị tại ô mới
                    int minOnPath = Math.min(val, grid[nr][nc]);
                    maxHeap.offer(new int[]{minOnPath, nr, nc});
                }
            }
        }
        return -1;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(M*N log(M*N)).
- Không gian (Space): O(M*N).

---

## 20. Course Schedule III (Lịch học khóa học III)
**Đề bài chi tiết:** Có `n` khóa học được biểu diễn bởi mảng `courses` trong đó `courses[i] = [duration_i, lastDay_i]` cho biết khóa học kéo dài `duration_i` ngày và phải hoàn thành chậm nhất vào ngày `lastDay_i`. Bạn có thể tham gia 1 khóa học mỗi thời điểm từ ngày 1. Tìm số lượng khóa học lớn nhất bạn có thể hoàn thành.

**Phân tích thuật toán:**
Đầu tiên, sắp xếp các khóa học theo `lastDay` tăng dần (ưu tiên học khóa nào kết thúc sớm hơn). Dùng một Max Heap để lưu thời lượng của các khóa học đã được nhận. Nếu thời gian hiện tại (`time`) vượt quá `lastDay` sau khi cộng thêm thời lượng của khóa học hiện tại, ta kiểm tra đỉnh của Max Heap (khoá học dài nhất đã nhận). Nếu khóa học hiện tại có thời gian ngắn hơn, ta huỷ bỏ khóa trên đỉnh Heap và thêm khóa này vào để dư dả thêm thời gian.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public int scheduleCourse(int[][] courses) {
        // Sắp xếp khóa học theo ngày hoàn thành trễ nhất tăng dần
        Arrays.sort(courses, (a, b) -> Integer.compare(a[1], b[1]));
        
        // Max Heap lưu thời gian của khóa học (duration)
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        int time = 0;
        
        for (int[] course : courses) {
            time += course[0];
            maxHeap.offer(course[0]);
            
            // Nếu tổng thời gian vượt hạn, vứt bỏ khóa học tốn nhiều thời gian nhất
            if (time > course[1]) {
                time -= maxHeap.poll();
            }
        }
        
        return maxHeap.size();
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N log N) - Với N là số lượng khóa học, chi phí O(N log N) cho sắp xếp và Heap.
- Không gian (Space): O(N) cho Priority Queue.

---

## 21. Maximize Capital (IPO)
**Đề bài chi tiết:** Cho `k` dự án có thể thực hiện, số vốn ban đầu `w`, mảng lợi nhuận `profits` và mảng vốn yêu cầu `capital` của mỗi dự án. Mục tiêu là chọn tối đa `k` dự án để tối đa hóa số vốn cuối cùng. Bạn chỉ có thể thực hiện một dự án nếu số vốn hiện tại của bạn lớn hơn hoặc bằng vốn yêu cầu của dự án đó.

**Phân tích thuật toán:**
Dùng hai Heap: một Min Heap lưu các dự án theo vốn yêu cầu (`capital`), và một Max Heap lưu các dự án theo lợi nhuận (`profit`). Đầu tiên, đẩy tất cả các dự án vào Min Heap. Sau đó, lặp `k` lần: đẩy tất cả các dự án từ Min Heap có vốn yêu cầu `<= w` vào Max Heap. Nếu Max Heap rỗng, nghĩa là không còn dự án nào có thể thực hiện, ta dừng lại. Ngược lại, lấy dự án có lợi nhuận cao nhất từ Max Heap, cộng lợi nhuận đó vào `w`.

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;

public class Solution {
    public int findMaximizedCapital(int k, int w, int[] profits, int[] capital) {
        int n = profits.length;
        // Min Heap theo vốn yêu cầu (capital)
        PriorityQueue<int[]> minCapitalHeap = new PriorityQueue<>((a, b) -> Integer.compare(a[0], b[0]));
        // Max Heap theo lợi nhuận (profit)
        PriorityQueue<int[]> maxProfitHeap = new PriorityQueue<>((a, b) -> Integer.compare(b[1], a[1]));
        
        for (int i = 0; i < n; i++) {
            minCapitalHeap.offer(new int[]{capital[i], profits[i]});
        }
        
        for (int i = 0; i < k; i++) {
            // Chuyển các dự án có thể thực hiện với vốn hiện tại sang Max Heap
            while (!minCapitalHeap.isEmpty() && minCapitalHeap.peek()[0] <= w) {
                maxProfitHeap.offer(minCapitalHeap.poll());
            }
            
            if (maxProfitHeap.isEmpty()) {
                break;
            }
            
            w += maxProfitHeap.poll()[1];
        }
        
        return w;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N log N + K log N). Sắp xếp N dự án vào heap tốn O(N log N). Rút và thêm tốn tối đa O(K log N).
- Không gian (Space): O(N) để lưu trữ các dự án.

---

## 22. Construct Target Array With Multiple Sums
**Đề bài chi tiết:** Cho mảng đích `target` chứa các số nguyên dương. Bắt đầu với một mảng `A` toàn số 1 có cùng kích thước. Mỗi bước, bạn có thể chọn một chỉ số `i`, tính tổng của tất cả các phần tử trong mảng `A`, và thay thế `A[i]` bằng tổng đó. Kiểm tra xem có thể tạo thành mảng `target` từ mảng `A` hay không.

**Phân tích thuật toán:**
Thực hiện quá trình ngược lại: đi từ `target` về mảng toàn số 1. Phần tử lớn nhất trong `target` là phần tử vừa được thay thế từ bước trước. Dùng Max Heap theo dõi phần tử lớn nhất. Lấy giá trị lớn nhất `maxVal` ra, tính tổng của các phần tử còn lại `restSum = totalSum - maxVal`. Giá trị trước đó của `maxVal` phải là `maxVal % restSum` (sử dụng phép chia lấy dư để tăng tốc thay vì trừ nhiều lần). Nếu `maxVal <= restSum` hoặc `restSum == 0`, mảng không hợp lệ (ngoại trừ khi `restSum == 1`). Sau đó, đẩy giá trị cũ vào lại Max Heap và cập nhật `totalSum`. Dừng khi phần tử lớn nhất bằng 1.

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;
import java.util.Collections;

public class Solution {
    public boolean isPossible(int[] target) {
        if (target.length == 1) return target[0] == 1;
        
        PriorityQueue<Long> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        long totalSum = 0;
        
        for (int num : target) {
            maxHeap.offer((long) num);
            totalSum += num;
        }
        
        while (maxHeap.peek() > 1) {
            long maxVal = maxHeap.poll();
            long restSum = totalSum - maxVal;
            
            if (restSum == 1) return true;
            if (restSum == 0 || maxVal <= restSum || maxVal % restSum == 0) return false;
            
            long oldVal = maxVal % restSum;
            maxHeap.offer(oldVal);
            totalSum = restSum + oldVal;
        }
        
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N log N). Do phép chia lấy dư, tốc độ giảm nhanh, độ sâu lặp không quá lớn.
- Không gian (Space): O(N).

---

## 23. Minimum Number of Refueling Stops (Số lần dừng đổ xăng tối thiểu)
**Đề bài chi tiết:** Một ô tô cần đi quãng đường `target` dặm với bình xăng ban đầu `startFuel`. Có các trạm xăng dọc đường `stations[i] = [position, fuel]`. Mỗi dặm đi tiêu tốn 1 đơn vị xăng. Tìm số lần dừng tối thiểu để bơm xăng, hoặc trả về -1 nếu không thể đến đích.

**Phân tích thuật toán:**
Sử dụng Max Heap để lưu lượng xăng của các trạm đã đi qua (nhưng chưa đổ). Mô phỏng quá trình chạy xe: bất cứ khi nào xe chạy đến hoặc qua một trạm xăng, ta thêm lượng xăng của trạm đó vào Max Heap. Khi xăng không đủ để đến trạm tiếp theo (hoặc đích), ta lấy lượng xăng lớn nhất từ Max Heap ra để "bơm" vào xe, tăng biến đếm số lần dừng. Nếu Max Heap rỗng mà vẫn hết xăng, trả về -1.

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;
import java.util.Collections;

public class Solution {
    public int minRefuelStops(int target, int startFuel, int[][] stations) {
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        int stops = 0;
        int currentFuel = startFuel;
        int prevPosition = 0;
        
        for (int[] station : stations) {
            int pos = station[0];
            int fuel = station[1];
            
            currentFuel -= (pos - prevPosition);
            
            while (currentFuel < 0 && !maxHeap.isEmpty()) {
                currentFuel += maxHeap.poll();
                stops++;
            }
            
            if (currentFuel < 0) return -1;
            
            maxHeap.offer(fuel);
            prevPosition = pos;
        }
        
        currentFuel -= (target - prevPosition);
        while (currentFuel < 0 && !maxHeap.isEmpty()) {
            currentFuel += maxHeap.poll();
            stops++;
        }
        
        if (currentFuel < 0) return -1;
        
        return stops;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N log N) với N là số lượng trạm xăng.
- Không gian (Space): O(N).

---

## 24. Rearrange String k Distance Apart (Sắp xếp lại chuỗi cách nhau k khoảng cách)
**Đề bài chi tiết:** Cho một chuỗi `s` và một số nguyên `k`. Hãy sắp xếp lại các ký tự trong chuỗi sao cho 2 ký tự giống nhau bất kỳ phải cách nhau ít nhất `k` khoảng cách. Trả về chuỗi kết quả, hoặc chuỗi rỗng nếu không thể.

**Phân tích thuật toán:**
Đếm tần suất các ký tự và đưa vào Max Heap theo tần suất giảm dần. Dùng một Queue để làm khoảng thời gian "chờ" cho các ký tự vừa được sử dụng (chúng phải cách nhau `k` bước mới được dùng lại). Lấy ký tự xuất hiện nhiều nhất từ Max Heap, thêm vào chuỗi kết quả, giảm tần suất và đưa vào Queue. Khi kích thước Queue đạt đến `k`, ta lấy ký tự ở đầu Queue đưa lại vào Max Heap nếu tần suất của nó vẫn lớn hơn 0. Nếu cuối cùng độ dài chuỗi tạo thành không bằng chuỗi gốc, trả về "".

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public String rearrangeString(String s, int k) {
        if (k == 0) return s;
        
        Map<Character, Integer> countMap = new HashMap<>();
        for (char c : s.toCharArray()) {
            countMap.put(c, countMap.getOrDefault(c, 0) + 1);
        }
        
        PriorityQueue<Character> maxHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare(countMap.get(b), countMap.get(a))
        );
        maxHeap.addAll(countMap.keySet());
        
        Queue<Character> waitQueue = new LinkedList<>();
        StringBuilder result = new StringBuilder();
        
        while (!maxHeap.isEmpty()) {
            char current = maxHeap.poll();
            result.append(current);
            countMap.put(current, countMap.get(current) - 1);
            waitQueue.offer(current);
            
            if (waitQueue.size() >= k) {
                char front = waitQueue.poll();
                if (countMap.get(front) > 0) {
                    maxHeap.offer(front);
                }
            }
        }
        
        return result.length() == s.length() ? result.toString() : "";
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N log A) với N là độ dài chuỗi, A là kích thước bảng chữ cái (thường A = 26).
- Không gian (Space): O(A).

---

## 25. Maximum Performance of a Team (Hiệu suất lớn nhất của nhóm)
**Đề bài chi tiết:** Có `n` kỹ sư, mỗi người có tốc độ `speed` và hiệu suất `efficiency`. Bạn cần chọn tối đa `k` kỹ sư để tạo thành một đội có "hiệu suất đội" lớn nhất. Hiệu suất đội bằng tổng tốc độ nhân với hiệu suất nhỏ nhất của kỹ sư trong đội. (Kết quả modulo $10^9 + 7$).

**Phân tích thuật toán:**
Ghép cặp tốc độ và hiệu suất, sau đó sắp xếp các kỹ sư theo hiệu suất giảm dần. Khi duyệt qua từng kỹ sư, hiệu suất của kỹ sư đó sẽ mặc định là "hiệu suất nhỏ nhất" của đội (vì đã sắp xếp giảm dần). Ta dùng một Min Heap để duy trì nhóm (tối đa `k` người) có `speed` lớn nhất. Thêm tốc độ của kỹ sư hiện tại vào Min Heap và cộng vào `speedSum`. Nếu số lượng người vượt quá `k`, loại bỏ người có tốc độ chậm nhất khỏi Heap. Cập nhật `max_performance`.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public int maxPerformance(int n, int[] speed, int[] efficiency, int k) {
        int[][] engineers = new int[n][2];
        for (int i = 0; i < n; i++) {
            engineers[i] = new int[]{speed[i], efficiency[i]};
        }
        
        // Sắp xếp theo hiệu suất giảm dần
        Arrays.sort(engineers, (a, b) -> Integer.compare(b[1], a[1]));
        
        // Min Heap duy trì tập hợp các tốc độ cao nhất
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        long speedSum = 0;
        long maxPerformance = 0;
        
        for (int[] engineer : engineers) {
            int currSpeed = engineer[0];
            int currEfficiency = engineer[1];
            
            minHeap.offer(currSpeed);
            speedSum += currSpeed;
            
            if (minHeap.size() > k) {
                speedSum -= minHeap.poll();
            }
            
            maxPerformance = Math.max(maxPerformance, speedSum * currEfficiency);
        }
        
        return (int) (maxPerformance % 1_000_000_007);
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N log N) cho sắp xếp, O(N log K) cho heap. Tổng: O(N log N).
- Không gian (Space): O(N) cho mảng engineers và O(K) cho Heap.

---

## 26. Smallest Range Covering Elements from K Lists (Phạm vi nhỏ nhất chứa phần tử từ K danh sách)
**Đề bài chi tiết:** Cho `k` danh sách các số nguyên đã được sắp xếp tăng dần. Tìm khoảng (range) `[a, b]` nhỏ nhất sao cho mỗi danh sách có ít nhất một phần tử nằm trong khoảng đó. Nếu có nhiều khoảng nhỏ nhất, trả về khoảng có `a` nhỏ hơn.

**Phân tích thuật toán:**
Dùng một Min Heap lưu trữ các phần tử cùng với chỉ số hàng và cột của chúng. Ban đầu, đẩy phần tử đầu tiên của mỗi danh sách vào Heap. Đồng thời duy trì giá trị lớn nhất hiện tại `maxVal` của các phần tử trong Heap. Tại mỗi bước, lấy phần tử nhỏ nhất từ Heap (là giá trị `minVal`). Khoảng bao phủ hiện tại là `[minVal, maxVal]`, cập nhật nếu nó nhỏ hơn khoảng tốt nhất trước đó. Sau đó, thay thế phần tử nhỏ nhất vừa rút ra bằng phần tử tiếp theo của cùng danh sách, đồng thời cập nhật `maxVal`. Lặp lại đến khi một danh sách bất kỳ hết phần tử.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public int[] smallestRange(List<List<Integer>> nums) {
        // Min Heap lưu {giá_trị, hàng, cột}
        PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> Integer.compare(a[0], b[0]));
        int maxVal = Integer.MIN_VALUE;
        
        for (int i = 0; i < nums.size(); i++) {
            int val = nums.get(i).get(0);
            minHeap.offer(new int[]{val, i, 0});
            maxVal = Math.max(maxVal, val);
        }
        
        int rangeStart = 0;
        int rangeEnd = Integer.MAX_VALUE;
        
        while (minHeap.size() == nums.size()) {
            int[] current = minHeap.poll();
            int minVal = current[0];
            int row = current[1];
            int col = current[2];
            
            if (maxVal - minVal < rangeEnd - rangeStart) {
                rangeStart = minVal;
                rangeEnd = maxVal;
            }
            
            if (col + 1 < nums.get(row).size()) {
                int nextVal = nums.get(row).get(col + 1);
                minHeap.offer(new int[]{nextVal, row, col + 1});
                maxVal = Math.max(maxVal, nextVal);
            }
        }
        
        return new int[]{rangeStart, rangeEnd};
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N log K) với N là tổng số phần tử trong tất cả K danh sách.
- Không gian (Space): O(K) cho Priority Queue.

---

## 27. Split Array into Consecutive Subsequences (Chia mảng thành các dãy con liên tiếp)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` đã sắp xếp tăng dần. Kiểm tra xem mảng có thể chia thành một hoặc nhiều dãy con sao cho mỗi dãy con chứa các số liên tiếp và có độ dài ít nhất là 3.

**Phân tích thuật toán:**
Ta có thể dùng một `HashMap` mà khóa là phần tử kết thúc của dãy con, và giá trị là một `PriorityQueue` lưu độ dài của các dãy con đó (Min Heap). Khi đọc tới phần tử `num`, ta kiểm tra xem có dãy con nào kết thúc ở `num - 1` không. Nếu có, ta lấy dãy con ngắn nhất (dưới đỉnh Min Heap), thêm `num` vào và cập nhật dãy con mới kết thúc ở `num`. Nếu không, ta bắt đầu một dãy con mới từ `num` có độ dài 1. Sau khi duyệt xong, kiểm tra xem mọi dãy con trong Hash Map có độ dài >= 3 hay không.

**Mã nguồn Java (Sử dụng Min Heap & HashMap):**
```java
import java.util.*;

public class Solution {
    public boolean isPossible(int[] nums) {
        Map<Integer, PriorityQueue<Integer>> tails = new HashMap<>();
        
        for (int num : nums) {
            PriorityQueue<Integer> pq = tails.getOrDefault(num - 1, new PriorityQueue<>());
            int len = 0;
            if (!pq.isEmpty()) {
                len = pq.poll();
                if (pq.isEmpty()) tails.remove(num - 1);
            }
            
            tails.putIfAbsent(num, new PriorityQueue<>());
            tails.get(num).offer(len + 1);
        }
        
        for (PriorityQueue<Integer> pq : tails.values()) {
            while (!pq.isEmpty()) {
                if (pq.poll() < 3) return false;
            }
        }
        
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N log N) - Mỗi phần tử được thêm và xóa khỏi Min Heap tương ứng.
- Không gian (Space): O(N) cho HashMap và Heap.

---

## 28. Furthest Building You Can Reach (Tòa nhà xa nhất có thể đạt đến)
**Đề bài chi tiết:** Bạn đang ở tòa nhà 0 và muốn đi xa nhất có thể. Tại mỗi bước sang tòa nhà tiếp theo, nếu độ cao cao hơn tòa nhà hiện tại, bạn phải dùng "gạch" (`bricks`) hoặc 1 cái "thang" (`ladder`). 1 thang giúp leo bất kỳ độ cao nào, còn 1 viên gạch giúp leo 1 đơn vị cao. Tìm vị trí xa nhất đạt được.

**Phân tích thuật toán:**
Luôn tối ưu bằng cách dùng thang cho các bước nhảy cao nhất. Dùng Min Heap lưu chênh lệch độ cao của các bước nhảy lên. Gặp bước nhảy lên, ta cho ngay độ chênh lệch vào Min Heap (mặc định dùng thang). Nếu số lượng phần tử trong Heap lớn hơn số thang `ladders`, nghĩa là ta buộc phải dùng gạch cho bước nhảy thấp nhất (phần tử gốc của Min Heap). Lấy nó ra và trừ vào số gạch. Nếu số gạch `< 0`, không thể đi tiếp, vị trí hiện tại là tối đa.

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;

public class Solution {
    public int furthestBuilding(int[] heights, int bricks, int ladders) {
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        
        for (int i = 0; i < heights.length - 1; i++) {
            int diff = heights[i + 1] - heights[i];
            
            if (diff > 0) {
                minHeap.offer(diff);
                
                // Nếu vượt quá số thang, lấy bước nhảy nhỏ nhất bù bằng gạch
                if (minHeap.size() > ladders) {
                    bricks -= minHeap.poll();
                }
                
                // Hết gạch không thể qua tòa nhà i+1
                if (bricks < 0) {
                    return i;
                }
            }
        }
        
        return heights.length - 1;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N log L) với L là số thang (L <= N).
- Không gian (Space): O(L) cho Min Heap.

---

## 29. Design Twitter (Thiết kế Twitter)
**Đề bài chi tiết:** Thiết kế một hệ thống Twitter đơn giản, cho phép người dùng đăng tweet, theo dõi/hủy theo dõi người dùng khác và xem 10 tweet gần đây nhất trong bảng tin (news feed) của bản thân và những người mình theo dõi.

**Phân tích thuật toán:**
Lưu trữ danh sách tweet của mỗi user (như một LinkedList hoặc ArrayList có chứa timestamp). Khi `getNewsFeed`, ta cần thu thập tweet từ user hiện tại và tất cả những người user này follow. Sử dụng Max Heap để merge các luồng tweet này (giống bài Merge K Sorted Lists) dựa trên timestamp. Chỉ lấy tối đa 10 phần tử mới nhất.

**Mã nguồn Java:**
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
    
    private Map<Integer, Tweet> userTweets;
    private Map<Integer, Set<Integer>> followees;

    public Twitter() {
        userTweets = new HashMap<>();
        followees = new HashMap<>();
    }
    
    public void postTweet(int userId, int tweetId) {
        Tweet tweet = new Tweet(tweetId);
        tweet.next = userTweets.get(userId);
        userTweets.put(userId, tweet);
    }
    
    public List<Integer> getNewsFeed(int userId) {
        PriorityQueue<Tweet> maxHeap = new PriorityQueue<>((a, b) -> Integer.compare(b.time, a.time));
        
        Set<Integer> users = followees.getOrDefault(userId, new HashSet<>());
        users.add(userId);
        
        for (int u : users) {
            Tweet t = userTweets.get(u);
            if (t != null) {
                maxHeap.offer(t);
            }
        }
        
        List<Integer> feed = new ArrayList<>();
        while (!maxHeap.isEmpty() && feed.size() < 10) {
            Tweet t = maxHeap.poll();
            feed.add(t.id);
            if (t.next != null) {
                maxHeap.offer(t.next);
            }
        }
        return feed;
    }
    
    public void follow(int followerId, int followeeId) {
        followees.putIfAbsent(followerId, new HashSet<>());
        followees.get(followerId).add(followeeId);
    }
    
    public void unfollow(int followerId, int followeeId) {
        if (followees.containsKey(followerId)) {
            followees.get(followerId).remove(followeeId);
        }
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): `postTweet`, `follow`, `unfollow` là O(1). `getNewsFeed` là O(K + 10 log K) với K là số người đang follow.
- Không gian (Space): O(N + T) với N là số user, T là tổng số tweet.

---

## 30. Maximum Number of Events That Can Be Attended (Số sự kiện tham gia nhiều nhất)
**Đề bài chi tiết:** Cho mảng các sự kiện `events` trong đó `events[i] = [startDay, endDay]`. Bạn có thể tham gia 1 sự kiện vào 1 ngày bất kỳ nằm trong khoảng `[startDay, endDay]`. Tìm số lượng tối đa các sự kiện bạn có thể tham gia.

**Phân tích thuật toán:**
Sắp xếp các sự kiện theo ngày bắt đầu tăng dần. Dùng một Min Heap để quản lý ngày kết thúc (`endDay`) của các sự kiện đang diễn ra (có thể tham gia). Lặp qua từng ngày `d` từ 1 đến 100,000 (hoặc tối ưu bằng cách tăng `d` dựa trên sự kiện). Với mỗi ngày: thêm tất cả sự kiện bắt đầu vào ngày đó vào Min Heap. Loại bỏ các sự kiện đã kết thúc trước ngày `d` (vì không thể tham gia nữa). Lấy một sự kiện có ngày kết thúc sớm nhất (trên đỉnh Heap) ra để tham gia và tăng biến đếm kết quả.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public int maxEvents(int[][] events) {
        Arrays.sort(events, (a, b) -> Integer.compare(a[0], b[0]));
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        
        int n = events.length;
        int count = 0;
        int eventIdx = 0;
        int d = 0; // Ngày hiện tại
        
        while (eventIdx < n || !minHeap.isEmpty()) {
            // Nhảy nhanh tới ngày có sự kiện nếu heap rỗng
            if (minHeap.isEmpty()) {
                d = events[eventIdx][0];
            }
            
            // Đẩy tất cả sự kiện bắt đầu vào ngày d vào Heap
            while (eventIdx < n && events[eventIdx][0] <= d) {
                minHeap.offer(events[eventIdx][1]);
                eventIdx++;
            }
            
            // Tham gia sự kiện kết thúc sớm nhất
            minHeap.poll();
            count++;
            d++;
            
            // Loại bỏ tất cả sự kiện đã hết hạn
            while (!minHeap.isEmpty() && minHeap.peek() < d) {
                minHeap.poll();
            }
        }
        
        return count;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N log N) cho việc sắp xếp mảng và thao tác trên Priority Queue.
- Không gian (Space): O(N) cho Priority Queue.

