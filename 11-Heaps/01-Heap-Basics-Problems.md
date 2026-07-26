# 01. Heap Basics Problems (Bài tập cơ bản về Heap)

Dưới đây là danh sách 30 bài toán về Heap, áp dụng từ các khái niệm Min Heap, Max Heap đến việc mô phỏng Priority Queue.

## 1. Kth Largest Element in an Array (Heap approach)
### Đề bài
Cho một mảng số nguyên `nums` chưa được sắp xếp và số nguyên `k`. Hãy trả về phần tử lớn thứ `k` trong mảng. Lưu ý rằng đó là phần tử lớn thứ `k` theo thứ tự sắp xếp, không phải phần tử phân biệt thứ `k`. Yêu cầu giải thuật có độ phức tạp thời gian là $O(N \log K)$.

### Phân tích thuật toán
Sử dụng **Min Heap** với kích thước tối đa là `k`.
Duyệt qua từng phần tử trong mảng, thêm phần tử vào Min Heap.
Nếu kích thước của Min Heap vượt quá `k`, ta loại bỏ phần tử nhỏ nhất (nằm ở gốc). 
Cuối cùng, phần tử nằm ở gốc của Min Heap chính là phần tử lớn thứ `k` của cả mảng.

### Mã nguồn Java
```java
import java.util.PriorityQueue;

public class Solution {
    public int findKthLargest(int[] nums, int k) {
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        for (int num : nums) {
            minHeap.offer(num);
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }
        return minHeap.peek();
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log K)$. Mỗi lần offer/poll mất $O(\log K)$, lặp $N$ lần.
- **Space Complexity:** $O(K)$ để duy trì heap.

---

## 2. Last Stone Weight
### Đề bài
Cho một mảng `stones`, mỗi phần tử đại diện cho trọng lượng của một viên đá. Mỗi lượt, ta chọn 2 viên đá nặng nhất và đập chúng vào nhau. Nếu `x == y`, cả hai viên biến mất. Nếu `x != y` (giả sử $x \le y$), viên đá $x$ biến mất và viên đá $y$ còn lại trọng lượng $y - x$. Trả về trọng lượng viên đá cuối cùng, nếu không còn viên nào trả về 0.

### Phân tích thuật toán
Dùng **Max Heap** để luôn lấy ra được hai viên đá lớn nhất một cách dễ dàng.
Liên tục pop 2 viên đá lớn nhất ra, so sánh, và push phần dư vào lại Heap (nếu có). 
Quá trình dừng khi Heap còn $\le 1$ viên đá.

### Mã nguồn Java
```java
import java.util.PriorityQueue;
import java.util.Collections;

public class Solution {
    public int lastStoneWeight(int[] stones) {
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        for (int stone : stones) {
            maxHeap.offer(stone);
        }
        
        while (maxHeap.size() > 1) {
            int y = maxHeap.poll();
            int x = maxHeap.poll();
            if (y > x) {
                maxHeap.offer(y - x);
            }
        }
        
        return maxHeap.isEmpty() ? 0 : maxHeap.peek();
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log N)$. Duyệt thêm $N$ phần tử vào heap, sau đó mô phỏng đập đá tối đa $N$ lần, mỗi lần $O(\log N)$.
- **Space Complexity:** $O(N)$ lưu tất cả các viên đá.

---

## 3. Relative Ranks
### Đề bài
Cho một mảng `score` chứa điểm số của $N$ vận động viên (các điểm số đôi một khác nhau). Gán huy chương cho top 3: "Gold Medal", "Silver Medal", "Bronze Medal". Những người còn lại nhận được chuỗi biểu diễn thứ hạng của họ (ví dụ "4", "5").

### Phân tích thuật toán
Sử dụng **Max Heap** chứa một cặp `(score, index)`. Pop lần lượt các phần tử ra để biết vận động viên nào xếp hạng bao nhiêu. Index giúp ghi kết quả vào đúng vị trí trong mảng String trả về.

### Mã nguồn Java
```java
import java.util.PriorityQueue;

public class Solution {
    public String[] findRelativeRanks(int[] score) {
        int n = score.length;
        String[] result = new String[n];
        // maxHeap lưu int[] chứa [điểm, index]
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) -> b[0] - a[0]);
        
        for (int i = 0; i < n; i++) {
            maxHeap.offer(new int[]{score[i], i});
        }
        
        int rank = 1;
        while (!maxHeap.isEmpty()) {
            int[] current = maxHeap.poll();
            int index = current[1];
            if (rank == 1) {
                result[index] = "Gold Medal";
            } else if (rank == 2) {
                result[index] = "Silver Medal";
            } else if (rank == 3) {
                result[index] = "Bronze Medal";
            } else {
                result[index] = String.valueOf(rank);
            }
            rank++;
        }
        return result;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log N)$ để push/pop $N$ phần tử khỏi heap.
- **Space Complexity:** $O(N)$ lưu result và phần tử trong heap.

---

## 4. Maximum Product of Two Elements in an Array
### Đề bài
Cho mảng số nguyên `nums`. Bạn cần chọn 2 chỉ số khác nhau $i$ và $j$ sao cho biểu thức `(nums[i]-1)*(nums[j]-1)` là lớn nhất. Trả về giá trị lớn nhất đó.

### Phân tích thuật toán
Sử dụng **Min Heap** cỡ 2 để tìm 2 số lớn nhất trong mảng. Hoặc đơn giản là duyệt tuyến tính tìm phần tử lớn nhất và lớn nhì. Để thực hành Heap, ta dùng Min Heap.

### Mã nguồn Java
```java
import java.util.PriorityQueue;

public class Solution {
    public int maxProduct(int[] nums) {
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        for (int num : nums) {
            minHeap.offer(num);
            if (minHeap.size() > 2) {
                minHeap.poll();
            }
        }
        int a = minHeap.poll();
        int b = minHeap.poll();
        return (a - 1) * (b - 1);
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log 2) = O(N)$.
- **Space Complexity:** $O(2) = O(1)$.

---

## 5. Minimum Operations to Halve Array Sum
### Đề bài
Cho một mảng `nums`. Mỗi bước, bạn có thể chọn một số trong mảng và chia đôi nó (chia nửa chính xác, có thể dùng số thập phân). Tính số thao tác ít nhất cần thiết để tổng của mảng ban đầu giảm đi ít nhất một nửa.

### Phân tích thuật toán
Để tổng giảm nhanh nhất, ta ưu tiên chia đôi số có giá trị lớn nhất hiện tại.
Dùng **Max Heap** (lưu số thực `Double`). Tại mỗi bước, pop giá trị Max, chia đôi, cập nhật lượng tổng đã giảm, rồi push lại vào heap. 

### Mã nguồn Java
```java
import java.util.PriorityQueue;
import java.util.Collections;

public class Solution {
    public int halveArray(int[] nums) {
        PriorityQueue<Double> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        double sum = 0;
        for (int num : nums) {
            sum += num;
            maxHeap.offer((double) num);
        }
        
        double target = sum / 2.0;
        double currentSum = sum;
        int operations = 0;
        
        while (currentSum > target) {
            double max = maxHeap.poll();
            double half = max / 2.0;
            currentSum -= half;
            maxHeap.offer(half);
            operations++;
        }
        
        return operations;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log N + K \log N)$, $K$ là số thao tác. Thực tế $K$ tương đối nhỏ.
- **Space Complexity:** $O(N)$ cho heap.

---

## 6. Take Gifts From the Richest Pile
### Đề bài
Bạn có mảng `gifts` đại diện cho các đống quà, và một số nguyên `k`. Trong $k$ giây, mỗi giây bạn lấy phần căn bậc hai (làm tròn xuống) của đống quà nhiều nhất và trả lại số lượng đó vào đống. Trả về tổng số quà còn lại sau $k$ giây.

### Phân tích thuật toán
Dùng **Max Heap** để lấy đống lớn nhất liên tục. Ở mỗi bước, poll đống lớn nhất $X$, thay bằng $\lfloor \sqrt{X} \rfloor$. Lặp $k$ lần.

### Mã nguồn Java
```java
import java.util.PriorityQueue;
import java.util.Collections;

public class Solution {
    public long pickGifts(int[] gifts, int k) {
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        for (int gift : gifts) {
            maxHeap.offer(gift);
        }
        
        for (int i = 0; i < k; i++) {
            int max = maxHeap.poll();
            maxHeap.offer((int) Math.sqrt(max));
        }
        
        long total = 0;
        while (!maxHeap.isEmpty()) {
            total += maxHeap.poll();
        }
        return total;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log N + K \log N)$. Build heap và làm `k` operations.
- **Space Complexity:** $O(N)$.

---

## 7. Make Array Zero by Subtracting Equal Amounts
### Đề bài
Cho một mảng nguyên không âm `nums`. Một thao tác cho phép tìm $X$ là số lượng $> 0$ nhỏ nhất trong mảng, và trừ $X$ từ toàn bộ các số $> 0$. Trả về số thao tác ít nhất để toàn bộ mảng thành số 0.

### Phân tích thuật toán
Bài toán thực ra tương đương với việc đếm số lượng các số phân biệt lớn hơn 0 trong mảng. Có thể dùng Set. Nhưng nếu áp dụng **Min Heap**, ta đẩy các giá trị dương vào, lấy min ra và cập nhật phần tử giảm dần (dùng biến `deducted` lưu tổng lượng đã trừ đi).

### Mã nguồn Java
```java
import java.util.PriorityQueue;

public class Solution {
    public int minimumOperations(int[] nums) {
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        for (int num : nums) {
            if (num > 0) {
                minHeap.offer(num);
            }
        }
        
        int operations = 0;
        int deducted = 0;
        
        while (!minHeap.isEmpty()) {
            int current = minHeap.poll();
            if (current > deducted) {
                deducted += (current - deducted);
                operations++;
            }
        }
        
        return operations;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log N)$.
- **Space Complexity:** $O(N)$.

---

## 8. Maximal Score After Applying K Operations
### Đề bài
Cho mảng `nums` và một số nguyên `k`. Mỗi lượt, chọn chỉ số $i$, cộng `nums[i]` vào điểm của bạn, sau đó cập nhật `nums[i] = ceil(nums[i] / 3)`. Tính điểm lớn nhất đạt được sau $k$ lượt.

### Phân tích thuật toán
Rõ ràng cần chọn số lớn nhất mỗi lượt để cộng vào điểm. Sử dụng **Max Heap** để lấy số lớn nhất, lấy xong đẩy kết quả `ceil(val / 3)` lại.

### Mã nguồn Java
```java
import java.util.PriorityQueue;
import java.util.Collections;

public class Solution {
    public long maxKelements(int[] nums, int k) {
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        for (int num : nums) {
            maxHeap.offer(num);
        }
        
        long score = 0;
        for (int i = 0; i < k; i++) {
            int max = maxHeap.poll();
            score += max;
            maxHeap.offer((int) Math.ceil(max / 3.0));
        }
        
        return score;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log N + K \log N)$.
- **Space Complexity:** $O(N)$.

---

## 9. Remove Stones to Minimize the Total
### Đề bài
Cho một mảng `piles` trong đó `piles[i]` là số lượng đá. Có `k` thao tác, mỗi thao tác loại bỏ $\lfloor piles[i] / 2 \rfloor$ đá từ `piles[i]`. Mục tiêu là cực tiểu hóa tổng số đá còn lại. Trả về tổng số đá min có thể.

### Phân tích thuật toán
Tương tự như các bài trước, ta muốn trừ đi nhiều nhất có thể mỗi bước. Ta dùng **Max Heap**. Lấy số lượng đá $X$ lớn nhất, loại bỏ $\lfloor X / 2 \rfloor$, số đá còn lại là $X - \lfloor X / 2 \rfloor$. Làm $k$ lần như vậy.

### Mã nguồn Java
```java
import java.util.PriorityQueue;
import java.util.Collections;

public class Solution {
    public int minStoneSum(int[] piles, int k) {
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        int sum = 0;
        for (int pile : piles) {
            sum += pile;
            maxHeap.offer(pile);
        }
        
        for (int i = 0; i < k; i++) {
            int max = maxHeap.poll();
            int removed = max / 2;
            sum -= removed;
            maxHeap.offer(max - removed);
        }
        
        return sum;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log N + K \log N)$.
- **Space Complexity:** $O(N)$.

---

## 10. Minimum Amount of Time to Fill Cups
### Đề bài
Bạn có máy bán nước có 3 loại cốc: cold, warm, hot. Số lượng cần lấy là `amount[0], amount[1], amount[2]`. Bạn có thể lấy 2 cốc khác loại cùng 1 lúc trong 1 giây, hoặc 1 cốc trong 1 giây. Tính số giây tối thiểu để lấy đủ cốc.

### Phân tích thuật toán
Mỗi bước ta lấy 2 cốc khác loại lớn nhất hiện tại để giảm số lượng cốc còn lại (dùng thuật toán tham lam).
Sử dụng **Max Heap** để theo dõi số lượng các loại. Ở mỗi giây, lấy 2 loại còn nhiều nhất trừ đi 1.

### Mã nguồn Java
```java
import java.util.PriorityQueue;
import java.util.Collections;

public class Solution {
    public int fillCups(int[] amount) {
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        for (int a : amount) {
            if (a > 0) {
                maxHeap.offer(a);
            }
        }
        
        int time = 0;
        while (maxHeap.size() > 1) {
            int first = maxHeap.poll();
            int second = maxHeap.poll();
            
            first--;
            second--;
            time++;
            
            if (first > 0) maxHeap.offer(first);
            if (second > 0) maxHeap.offer(second);
        }
        
        if (!maxHeap.isEmpty()) {
            time += maxHeap.poll();
        }
        
        return time;
    }
}
```

### Độ phức tạp
- **Time Complexity:** Cụ thể lượng cốc max là 100, số vòng lặp cũng nhỏ. Heap size max = 3. Độ phức tạp $O(M)$ với $M$ là max phần tử, rất nhanh $O(1)$.
- **Space Complexity:** $O(1)$.

---

## 11. Kth Smallest Element in a Sorted Matrix
### Đề bài
Cho một ma trận `matrix` kích thước $N \times N$ trong đó mỗi hàng và mỗi cột được sắp xếp theo thứ tự tăng dần, và một số nguyên `k`. Hãy trả về phần tử nhỏ thứ `k` trong ma trận. Cần lưu ý rằng đó là phần tử nhỏ thứ `k` theo thứ tự sắp xếp, không phải phần tử phân biệt thứ `k`. Yêu cầu giải thuật có độ phức tạp về bộ nhớ thấp.

### Phân tích thuật toán
Vì mỗi hàng và mỗi cột đều đã được sắp xếp, bài toán tương đương với việc gộp $N$ danh sách đã sắp xếp. Sử dụng **Min Heap** để lưu trữ các phần tử cùng với tọa độ `(row, col)`.
Đầu tiên, ta đưa các phần tử ở cột đầu tiên (hoặc hàng đầu tiên) vào Min Heap. Sau đó, lặp $k-1$ lần: lấy phần tử nhỏ nhất ra khỏi Heap và đẩy phần tử tiếp theo trong cùng hàng (hoặc cột) vào Heap. Sau $k-1$ vòng lặp, phần tử nằm ở gốc của Heap chính là phần tử nhỏ thứ `k`.

### Mã nguồn Java
```java
import java.util.PriorityQueue;

public class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> a[0] - b[0]);
        
        for (int i = 0; i < Math.min(n, k); i++) {
            minHeap.offer(new int[]{matrix[i][0], i, 0});
        }
        
        int result = 0;
        for (int i = 0; i < k; i++) {
            int[] current = minHeap.poll();
            result = current[0];
            int row = current[1];
            int col = current[2];
            
            if (col + 1 < n) {
                minHeap.offer(new int[]{matrix[row][col + 1], row, col + 1});
            }
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(K \log(\min(N, K)))$. Xây dựng heap mất $O(\min(N, K))$, lấy và thêm phần tử mất $K$ lần $O(\log(\min(N, K)))$.
- **Space Complexity:** $O(\min(N, K))$ để duy trì heap.

---

## 12. K Closest Points to Origin
### Đề bài
Cho một mảng các điểm `points` trong đó `points[i] = [xi, yi]` đại diện cho một điểm trên mặt phẳng tọa độ $XY$ và một số nguyên `k`. Hãy trả về `k` điểm gần nhất với gốc tọa độ $(0, 0)$.
Khoảng cách giữa hai điểm trên mặt phẳng được tính bằng khoảng cách Euclidean: $\sqrt{x^2 + y^2}$.

### Phân tích thuật toán
Sử dụng **Max Heap** có kích thước tối đa là `k` để lưu trữ các điểm.
Duyệt qua từng điểm trong mảng, tính bình phương khoảng cách đến gốc tọa độ (không cần khai căn để tránh sai số thập phân) và đưa vào Max Heap.
Nếu kích thước của Max Heap vượt quá `k`, ta loại bỏ phần tử có khoảng cách lớn nhất (nằm ở gốc). Cuối cùng, `k` phần tử còn lại trong Heap chính là `k` điểm gần nhất.

### Mã nguồn Java
```java
import java.util.PriorityQueue;

public class Solution {
    public int[][] kClosest(int[][] points, int k) {
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) -> 
            (b[0] * b[0] + b[1] * b[1]) - (a[0] * a[0] + a[1] * a[1])
        );
        
        for (int[] point : points) {
            maxHeap.offer(point);
            if (maxHeap.size() > k) {
                maxHeap.poll();
            }
        }
        
        int[][] result = new int[k][2];
        int index = 0;
        while (!maxHeap.isEmpty()) {
            result[index++] = maxHeap.poll();
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log K)$. Mỗi lần thêm vào heap mất $O(\log K)$, lặp $N$ lần.
- **Space Complexity:** $O(K)$ để lưu tối đa `k` phần tử trong Max Heap.

---

## 13. Sort Characters By Frequency
### Đề bài
Cho một chuỗi `s`, sắp xếp chuỗi đó theo thứ tự giảm dần của tần suất xuất hiện các ký tự. Nếu có nhiều ký tự có cùng tần suất, chúng có thể xuất hiện theo bất kỳ thứ tự nào. Trả về chuỗi sau khi sắp xếp.

### Phân tích thuật toán
Sử dụng **HashMap** để đếm tần suất xuất hiện của từng ký tự trong chuỗi `s`.
Sử dụng **Max Heap** để lưu các ký tự, sắp xếp theo tần suất xuất hiện giảm dần.
Lấy lần lượt các ký tự ra khỏi Max Heap, lặp lại số lần bằng với tần suất xuất hiện của chúng và nối vào chuỗi kết quả (sử dụng `StringBuilder`).

### Mã nguồn Java
```java
import java.util.Map;
import java.util.HashMap;
import java.util.PriorityQueue;

public class Solution {
    public String frequencySort(String s) {
        Map<Character, Integer> freqMap = new HashMap<>();
        for (char c : s.toCharArray()) {
            freqMap.put(c, freqMap.getOrDefault(c, 0) + 1);
        }
        
        PriorityQueue<Character> maxHeap = new PriorityQueue<>(
            (a, b) -> freqMap.get(b) - freqMap.get(a)
        );
        
        maxHeap.addAll(freqMap.keySet());
        
        StringBuilder sb = new StringBuilder();
        while (!maxHeap.isEmpty()) {
            char c = maxHeap.poll();
            int freq = freqMap.get(c);
            for (int i = 0; i < freq; i++) {
                sb.append(c);
            }
        }
        
        return sb.toString();
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N + U \log U)$, trong đó $N$ là độ dài chuỗi, $U$ là số lượng ký tự phân biệt (tối đa 62 với chữ cái và số, coi như hằng số $O(1)$). Do đó thời gian gần như là $O(N)$.
- **Space Complexity:** $O(U)$ lưu Map và Heap, có thể coi là $O(1)$ nếu charset giới hạn.

---

## 14. Top K Frequent Elements
### Đề bài
Cho một mảng số nguyên `nums` và số nguyên `k`, trả về `k` phần tử xuất hiện nhiều nhất. Bạn có thể trả về câu trả lời theo bất kỳ thứ tự nào. Giải thuật phải có độ phức tạp thời gian tốt hơn $O(N \log N)$.

### Phân tích thuật toán
Sử dụng **HashMap** để đếm số lần xuất hiện của mỗi phần tử.
Sử dụng **Min Heap** (kích thước tối đa $k$) lưu trữ các phần tử, so sánh dựa trên tần suất xuất hiện.
Nếu kích thước Heap vượt quá $k$, loại bỏ phần tử có tần suất nhỏ nhất ở gốc Heap. Các phần tử còn lại sẽ là những phần tử có tần suất cao nhất.

### Mã nguồn Java
```java
import java.util.Map;
import java.util.HashMap;
import java.util.PriorityQueue;

public class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freqMap = new HashMap<>();
        for (int num : nums) {
            freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);
        }
        
        PriorityQueue<Integer> minHeap = new PriorityQueue<>(
            (a, b) -> freqMap.get(a) - freqMap.get(b)
        );
        
        for (int key : freqMap.keySet()) {
            minHeap.offer(key);
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }
        
        int[] result = new int[k];
        for (int i = 0; i < k; i++) {
            result[i] = minHeap.poll();
        }
        return result;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N + U \log K)$ với $N$ là số phần tử của `nums`, $U$ là số lượng phần tử phân biệt.
- **Space Complexity:** $O(U + K)$ lưu trữ Hash Map và Min Heap.

---

## 15. Top K Frequent Words
### Đề bài
Cho một mảng chuỗi `words` và một số nguyên `k`. Hãy trả về `k` từ xuất hiện nhiều nhất.
Kết quả trả về phải được sắp xếp theo tần suất từ cao xuống thấp. Nếu có các từ cùng tần suất xuất hiện, hãy sắp xếp chúng theo thứ tự từ điển (Lexicographical order).

### Phân tích thuật toán
Dùng **HashMap** đếm số lượng xuất hiện của mỗi từ.
Dùng **Min Heap** cỡ $k$ để giữ lại top $k$ từ. Lớp Comparator cần được định nghĩa lại:
- Nếu tần suất khác nhau, ưu tiên phần tử có tần suất nhỏ hơn nằm trên đỉnh (để loại bỏ).
- Nếu tần suất giống nhau, từ nào có **thứ tự từ điển lớn hơn** thì phải nằm trên đỉnh (nghĩa là nó kém ưu tiên hơn, dễ bị đẩy ra hơn).
Sau khi xây dựng xong Heap, ta pop các từ ra và đảo ngược thứ tự lại (vì đây là Min Heap).

### Mã nguồn Java
```java
import java.util.Map;
import java.util.HashMap;
import java.util.PriorityQueue;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;

public class Solution {
    public List<String> topKFrequent(String[] words, int k) {
        Map<String, Integer> freqMap = new HashMap<>();
        for (String word : words) {
            freqMap.put(word, freqMap.getOrDefault(word, 0) + 1);
        }
        
        PriorityQueue<String> minHeap = new PriorityQueue<>((w1, w2) -> {
            int freq1 = freqMap.get(w1);
            int freq2 = freqMap.get(w2);
            if (freq1 == freq2) {
                // Tần suất giống nhau, đưa từ có thứ tự từ điển lớn hơn lên đầu để dễ loại bỏ
                return w2.compareTo(w1);
            }
            // Tần suất khác nhau, đưa từ có tần suất nhỏ hơn lên đầu để loại bỏ
            return freq1 - freq2;
        });
        
        for (String word : freqMap.keySet()) {
            minHeap.offer(word);
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }
        
        List<String> result = new ArrayList<>();
        while (!minHeap.isEmpty()) {
            result.add(minHeap.poll());
        }
        Collections.reverse(result);
        
        return result;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \cdot L + U \log K \cdot L)$ với $N$ là số lượng từ, $U$ là số từ phân biệt, $L$ là độ dài trung bình của chuỗi.
- **Space Complexity:** $O(U \cdot L)$ lưu HashMap và Heap.

---

## 16. Find K Pairs with Smallest Sums
### Đề bài
Cho hai mảng số nguyên `nums1` và `nums2` được sắp xếp theo thứ tự tăng dần, và một số nguyên `k`. Hãy xác định `k` cặp `(u, v)` (trong đó `u` từ `nums1`, `v` từ `nums2`) sao cho tổng `u + v` là nhỏ nhất.

### Phân tích thuật toán
Tương tự như hợp nhất mảng đã sắp xếp. Đầu tiên, ghép phần tử đầu tiên của `nums2` với các phần tử của `nums1` (tối đa `k` phần tử) và đẩy vào **Min Heap**.
Heap lưu chỉ số dưới dạng `[index1, index2]`. Ở mỗi bước, ta lấy cặp có tổng nhỏ nhất ra, ghi nhận kết quả, sau đó lấy cặp kế tiếp `[index1, index2 + 1]` (giữ nguyên ở `nums1` nhưng tiến lên 1 bước ở `nums2`) đẩy vào Heap.

### Mã nguồn Java
```java
import java.util.PriorityQueue;
import java.util.List;
import java.util.ArrayList;
import java.util.Arrays;

public class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> result = new ArrayList<>();
        if (nums1.length == 0 || nums2.length == 0 || k == 0) return result;
        
        // Min Heap lưu mảng int[]{chỉ số trong nums1, chỉ số trong nums2}
        PriorityQueue<int[]> minHeap = new PriorityQueue<>(
            (a, b) -> (nums1[a[0]] + nums2[a[1]]) - (nums1[b[0]] + nums2[b[1]])
        );
        
        for (int i = 0; i < Math.min(nums1.length, k); i++) {
            minHeap.offer(new int[]{i, 0});
        }
        
        while (k > 0 && !minHeap.isEmpty()) {
            int[] current = minHeap.poll();
            int i = current[0];
            int j = current[1];
            
            result.add(Arrays.asList(nums1[i], nums2[j]));
            
            if (j + 1 < nums2.length) {
                minHeap.offer(new int[]{i, j + 1});
            }
            k--;
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(K \log (\min(N, K)))$. Tạo Heap ban đầu mất $O(\min(N, K))$, sau đó lấy $K$ phần tử, mỗi lần thêm/xóa tốn $O(\log(\min(N, K)))$.
- **Space Complexity:** $O(\min(N, K))$ để lưu vào Heap.

---

## 17. Merge K Sorted Lists
### Đề bài
Cho mảng chứa `k` danh sách liên kết (`LinkedList`), mỗi danh sách đã được sắp xếp theo thứ tự tăng dần. Ghép tất cả các danh sách liên kết đó thành một danh sách liên kết đã sắp xếp và trả về danh sách đó.

### Phân tích thuật toán
Đưa tất cả các `Node` ở vị trí đầu của $K$ danh sách vào một **Min Heap**. 
Lấy Node nhỏ nhất ra khỏi Heap để gắn vào danh sách kết quả, và đưa `Node.next` của nó vào Heap. Lặp lại cho đến khi Heap trống.

### Mã nguồn Java
```java
import java.util.PriorityQueue;

// Giả định định nghĩa ListNode
class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

public class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) return null;
        
        PriorityQueue<ListNode> minHeap = new PriorityQueue<>((a, b) -> a.val - b.val);
        
        for (ListNode node : lists) {
            if (node != null) {
                minHeap.offer(node);
            }
        }
        
        ListNode dummy = new ListNode(0);
        ListNode current = dummy;
        
        while (!minHeap.isEmpty()) {
            ListNode smallest = minHeap.poll();
            current.next = smallest;
            current = current.next;
            
            if (smallest.next != null) {
                minHeap.offer(smallest.next);
            }
        }
        
        return dummy.next;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log K)$ với $N$ là tổng số lượng Node trong tất cả các danh sách và $K$ là số lượng danh sách (kích thước Heap).
- **Space Complexity:** $O(K)$ để duy trì Priority Queue.

---

## 18. Task Scheduler
### Đề bài
Cho một mảng ký tự `tasks` biểu diễn các công việc mà CPU cần thực hiện, và một số nguyên `n` là thời gian làm mát (cool down). Khoảng cách giữa 2 công việc giống nhau phải ít nhất là `n` đơn vị thời gian. Trong thời gian làm mát, CPU có thể làm công việc khác hoặc nghỉ (`idle`). Tính thời gian tối thiểu để hoàn thành tất cả các công việc.

### Phân tích thuật toán
Chúng ta muốn ưu tiên thực hiện các công việc còn tồn đọng nhiều nhất để tránh bị kẹt các công việc này vào cuối cùng.
Dùng **Max Heap** để lưu trữ tần suất các công việc.
Dùng một **Queue** để đóng vai trò như chu kỳ cool down: lưu các cặp `(tần suất còn lại, thời gian có thể quay lại Max Heap)`.
Mỗi đơn vị thời gian, ta lấy một công việc ở Max Heap giảm tần suất đi 1 và đưa vào Queue. Khi công việc trong Queue đã vượt qua khoảng thời gian `n`, ta đưa nó quay lại Max Heap.

### Mã nguồn Java
```java
import java.util.Map;
import java.util.HashMap;
import java.util.PriorityQueue;
import java.util.Queue;
import java.util.LinkedList;

public class Solution {
    public int leastInterval(char[] tasks, int n) {
        Map<Character, Integer> freqMap = new HashMap<>();
        for (char task : tasks) {
            freqMap.put(task, freqMap.getOrDefault(task, 0) + 1);
        }
        
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> b - a);
        maxHeap.addAll(freqMap.values());
        
        // Queue lưu trữ mảng int[]{tần_suất_còn_lại, thời_gian_sẵn_sàng}
        Queue<int[]> queue = new LinkedList<>();
        int time = 0;
        
        while (!maxHeap.isEmpty() || !queue.isEmpty()) {
            time++;
            
            if (!maxHeap.isEmpty()) {
                int count = maxHeap.poll() - 1;
                if (count > 0) {
                    queue.offer(new int[]{count, time + n});
                }
            }
            
            if (!queue.isEmpty() && queue.peek()[1] == time) {
                maxHeap.offer(queue.poll()[0]);
            }
        }
        
        return time;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(M)$ với $M$ là tổng thời gian hoàn thành. Do số task tối đa là 26 nên các thao tác Heap xem như $O(1)$.
- **Space Complexity:** $O(1)$ chỉ lưu tối đa 26 loại task.

---

## 19. Reorganize String
### Đề bài
Cho chuỗi `s`, sắp xếp lại các ký tự trong chuỗi sao cho không có hai ký tự liền kề nào giống nhau. Trả về chuỗi sau khi sắp xếp, nếu không thể, hãy trả về chuỗi rỗng `""`.

### Phân tích thuật toán
Tương tự như bài toán lập lịch, ký tự nào xuất hiện nhiều nhất cần được ưu tiên dùng sớm.
Sử dụng **Max Heap** để lưu các ký tự giảm dần theo số lượng. 
Lấy ký tự nhiều nhất từ Max Heap gắn vào kết quả. Khi dùng 1 ký tự, không được đẩy nó ngay lại vào Heap mà phải để dành (lưu vào biến tạm) cho tới lần lặp kế tiếp để bảo đảm không bị đứng liền kề.

### Mã nguồn Java
```java
import java.util.Map;
import java.util.HashMap;
import java.util.PriorityQueue;

public class Solution {
    public String reorganizeString(String s) {
        Map<Character, Integer> freqMap = new HashMap<>();
        for (char c : s.toCharArray()) {
            freqMap.put(c, freqMap.getOrDefault(c, 0) + 1);
        }
        
        PriorityQueue<Character> maxHeap = new PriorityQueue<>(
            (a, b) -> freqMap.get(b) - freqMap.get(a)
        );
        maxHeap.addAll(freqMap.keySet());
        
        StringBuilder result = new StringBuilder();
        // Biến tạm để giữ ký tự vừa dùng ở bước trước
        char prevChar = '#';
        int prevFreq = 0;
        
        while (!maxHeap.isEmpty()) {
            char current = maxHeap.poll();
            result.append(current);
            
            // Nếu có ký tự tạm chờ thì đưa vào lại Heap
            if (prevFreq > 0) {
                maxHeap.offer(prevChar);
            }
            
            // Cập nhật biến tạm với ký tự hiện tại
            freqMap.put(current, freqMap.get(current) - 1);
            prevChar = current;
            prevFreq = freqMap.get(current);
        }
        
        // Nếu độ dài chưa đủ => không thể sắp xếp
        if (result.length() != s.length()) {
            return "";
        }
        return result.toString();
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log U)$ với $N$ là độ dài chuỗi và $U$ là số lượng ký tự phân biệt (tối đa 26). Do đó tương đương $O(N)$.
- **Space Complexity:** $O(U) = O(1)$ để lưu Hash Map và Max Heap.

---

## 20. Construct String With Repeat Limit
### Đề bài
Cho chuỗi `s` và một số nguyên `repeatLimit`. Hãy xây dựng một chuỗi mới từ các ký tự trong `s` sao cho không có bất kỳ ký tự nào xuất hiện liên tiếp quá `repeatLimit` lần. Trả về chuỗi kết quả có thứ tự từ điển lớn nhất có thể.

### Phân tích thuật toán
Để đạt thứ tự từ điển lớn nhất, ta ưu tiên sử dụng các ký tự có giá trị từ điển lớn nhất có thể (z -> a). Dùng **Max Heap** để lưu ký tự theo chiều giảm dần.
Lấy ký tự ưu tiên nhất ra dùng tối đa `repeatLimit` lần. Nếu vẫn còn ký tự này, ta không được dùng tiếp nó ngay, mà phải lấy ký tự ưu tiên **thứ hai** ra để làm "vách ngăn" (chỉ dùng 1 lần), sau đó mới đưa ký tự thứ nhất vào lại Heap để tiếp tục. 

### Mã nguồn Java
```java
import java.util.Map;
import java.util.HashMap;
import java.util.PriorityQueue;

public class Solution {
    public String repeatLimitedString(String s, int repeatLimit) {
        Map<Character, Integer> freqMap = new HashMap<>();
        for (char c : s.toCharArray()) {
            freqMap.put(c, freqMap.getOrDefault(c, 0) + 1);
        }
        
        PriorityQueue<Character> maxHeap = new PriorityQueue<>((a, b) -> b - a);
        maxHeap.addAll(freqMap.keySet());
        
        StringBuilder result = new StringBuilder();
        
        while (!maxHeap.isEmpty()) {
            char current = maxHeap.poll();
            int count = freqMap.get(current);
            
            // Số lần dùng ký tự này không quá repeatLimit
            int use = Math.min(count, repeatLimit);
            for (int i = 0; i < use; i++) {
                result.append(current);
            }
            
            freqMap.put(current, count - use);
            
            if (freqMap.get(current) > 0) {
                if (maxHeap.isEmpty()) {
                    break; // Không còn ký tự nào để ngăn cách
                }
                
                // Kéo ký tự thứ hai ra làm vách ngăn
                char next = maxHeap.poll();
                result.append(next);
                freqMap.put(next, freqMap.get(next) - 1);
                
                if (freqMap.get(next) > 0) {
                    maxHeap.offer(next);
                }
                
                // Đẩy lại ký tự thứ nhất vào để xử lý tiếp
                maxHeap.offer(current);
            }
        }
        
        return result.toString();
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log U)$ với $N$ là độ dài chuỗi và $U \le 26$. Vì $U \le 26$, thời gian thực thi xấp xỉ $O(N)$.
- **Space Complexity:** $O(U) = O(1)$ để lưu tần suất và Priority Queue.

---

## 21. Seat Reservation Manager
### Đề bài
Thiết kế một hệ thống quản lý đặt chỗ ngồi gồm `n` ghế được đánh số từ `1` đến `n`.
Yêu cầu hỗ trợ các thao tác:
- `SeatManager(int n)`: Khởi tạo đối tượng quản lý với `n` ghế trống.
- `int reserve()`: Lấy ghế trống có số hiệu nhỏ nhất, đặt chỗ và trả về số hiệu của ghế đó.
- `void unreserve(int seatNumber)`: Hủy đặt chỗ của ghế `seatNumber`, làm cho nó trống trở lại.

### Phân tích thuật toán
Sử dụng một **Min Heap** để lưu trữ các ghế trống. Ban đầu, đẩy tất cả các ghế từ `1` đến `n` vào Heap.
- Khi gọi `reserve()`, ta `poll()` phần tử nhỏ nhất ra khỏi Min Heap.
- Khi gọi `unreserve(int seatNumber)`, ta đẩy lại `seatNumber` vào Min Heap bằng thao tác `offer()`.

### Mã nguồn Java
```java
import java.util.PriorityQueue;

class SeatManager {
    private PriorityQueue<Integer> availableSeats;

    public SeatManager(int n) {
        availableSeats = new PriorityQueue<>();
        for (int i = 1; i <= n; i++) {
            availableSeats.offer(i);
        }
    }
    
    public int reserve() {
        return availableSeats.poll();
    }
    
    public void unreserve(int seatNumber) {
        availableSeats.offer(seatNumber);
    }
}
```

### Độ phức tạp
- **Time Complexity:** Khởi tạo `SeatManager` tốn $O(N \log N)$ (hoặc $O(N)$ nếu build tối ưu), `reserve` và `unreserve` tốn $O(\log N)$ mỗi lần gọi.
- **Space Complexity:** $O(N)$ để lưu trữ $N$ ghế trong Heap.

---

## 22. Kth Smallest Prime Fraction
### Đề bài
Cho một mảng `arr` đã sắp xếp chứa các số nguyên tố và số `1` (các số đều là duy nhất), và một số nguyên `k`. Hãy xét tất cả các phân số có dạng `arr[i] / arr[j]` với $0 \le i < j < \text{arr.length}$. Trả về phân số nhỏ thứ `k` dưới dạng mảng 2 phần tử `[arr[i], arr[j]]`.

### Phân tích thuật toán
Để tìm phân số nhỏ nhất, ta chia các phân số thành các mảng đã được sắp xếp. Vì mảng `arr` tăng dần, nên tại một mẫu số `arr[j]` cố định, các phân số `arr[0]/arr[j] < arr[1]/arr[j] < ... < arr[j-1]/arr[j]`.
Dùng **Min Heap** để lưu trữ giá trị thập phân của phân số, kèm theo chỉ số tử số và mẫu số. Đầu tiên đẩy các phân số nhỏ nhất của từng mảng (tức là `arr[0]/arr[j]` với mọi $j$) vào Heap. Sau đó, ở mỗi vòng lặp (pop $k-1$ lần), ta thay thế nó bằng phân số tiếp theo trong cùng "mảng" (tức là tăng chỉ số tử số lên 1). Cuối cùng, đỉnh của Heap là đáp án.

### Mã nguồn Java
```java
import java.util.PriorityQueue;

public class Solution {
    public int[] kthSmallestPrimeFraction(int[] arr, int k) {
        int n = arr.length;
        // Heap lưu mảng double[]{giá_trị_phân_số, chỉ_số_tử_số, chỉ_số_mẫu_số}
        PriorityQueue<double[]> minHeap = new PriorityQueue<>((a, b) -> Double.compare(a[0], b[0]));
        
        for (int j = 1; j < n; j++) {
            minHeap.offer(new double[]{ (double) arr[0] / arr[j], 0, j });
        }
        
        for (int i = 0; i < k - 1; i++) {
            double[] current = minHeap.poll();
            int numIndex = (int) current[1];
            int denIndex = (int) current[2];
            
            if (numIndex + 1 < denIndex) {
                minHeap.offer(new double[]{
                    (double) arr[numIndex + 1] / arr[denIndex], 
                    numIndex + 1, 
                    denIndex
                });
            }
        }
        
        double[] result = minHeap.poll();
        return new int[]{ arr[(int) result[1]], arr[(int) result[2]] };
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log N + K \log N)$. $O(N \log N)$ để đưa các phần tử ban đầu vào Heap, lấy phần tử ra tốn $O(K \log N)$.
- **Space Complexity:** $O(N)$ để lưu Heap.

---

## 23. The Number of the Smallest Unoccupied Chair
### Đề bài
Có một bữa tiệc với `n` người bạn và vô số ghế xếp thành hàng được đánh số $0, 1, 2, \dots$. Mảng `times[i] = [arrival_i, leaving_i]` biểu diễn thời gian đến và đi của người bạn thứ `i`. Khi đến, họ sẽ ngồi vào chiếc ghế trống có số hiệu nhỏ nhất. Trả về số hiệu ghế mà người bạn `targetFriend` sẽ ngồi.

### Phân tích thuật toán
Sử dụng **hai Min Heap**:
1. `availableChairs` lưu các số hiệu ghế đang trống.
2. `occupiedChairs` lưu thông tin ghế đang bị chiếm dưới dạng `[thời_gian_rời_đi, số_hiệu_ghế]`.
Trước hết, ta sắp xếp mảng `times` theo thời gian đến nhưng phải ghi nhớ chỉ số ban đầu của mỗi người. Tại thời điểm một người đến, ta kiểm tra Heap `occupiedChairs` và giải phóng tất cả các ghế của những người rời đi trước hoặc bằng thời gian đến này. Sau đó lấy ghế nhỏ nhất từ `availableChairs` cho người đến hiện tại.

### Mã nguồn Java
```java
import java.util.PriorityQueue;
import java.util.Arrays;

public class Solution {
    public int smallestChair(int[][] times, int targetFriend) {
        int n = times.length;
        int[][] events = new int[n][3];
        for (int i = 0; i < n; i++) {
            events[i][0] = times[i][0]; // arrival
            events[i][1] = times[i][1]; // leaving
            events[i][2] = i;           // friend id
        }
        
        Arrays.sort(events, (a, b) -> a[0] - b[0]);
        
        PriorityQueue<Integer> availableChairs = new PriorityQueue<>();
        for (int i = 0; i < n; i++) {
            availableChairs.offer(i);
        }
        
        // Heap lưu mảng int[]{leavingTime, chairId}
        PriorityQueue<int[]> occupiedChairs = new PriorityQueue<>((a, b) -> a[0] - b[0]);
        
        for (int[] event : events) {
            int arrival = event[0];
            int leaving = event[1];
            int friendId = event[2];
            
            // Giải phóng ghế của những người đã rời đi
            while (!occupiedChairs.isEmpty() && occupiedChairs.peek()[0] <= arrival) {
                availableChairs.offer(occupiedChairs.poll()[1]);
            }
            
            int assignedChair = availableChairs.poll();
            if (friendId == targetFriend) {
                return assignedChair;
            }
            
            occupiedChairs.offer(new int[]{leaving, assignedChair});
        }
        
        return -1;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log N)$ cho việc sắp xếp và các thao tác Heap (thêm/xóa $N$ lần).
- **Space Complexity:** $O(N)$ lưu 2 Heap và mảng sự kiện.

---

## 24. Maximum Number of Eaten Apples
### Đề bài
Cho hai mảng `apples` và `days`. Tại ngày thứ `i`, cây cho `apples[i]` quả táo và chúng sẽ thối hỏng vào ngày `i + days[i]`. Mỗi ngày bạn chỉ có thể ăn tối đa 1 quả táo (miễn là nó chưa hỏng). Trả về số táo nhiều nhất có thể ăn được.

### Phân tích thuật toán
Quy tắc tham lam: Ưu tiên ăn quả táo nào sẽ hỏng sớm nhất.
Dùng **Min Heap** để lưu `[ngày_hỏng, số_lượng_táo]`.
Vòng lặp chạy theo từng ngày, tại mỗi ngày `i`, thêm táo mới thu hoạch vào Heap. Sau đó, loại bỏ các táo đã hỏng (ngày_hỏng $\le$ ngày hiện tại) hoặc số lượng táo bằng 0 ra khỏi Heap. Ăn một quả táo từ mẻ hỏng sớm nhất và giảm số lượng đi 1. Lặp tiếp đến khi Heap trống hoàn toàn.

### Mã nguồn Java
```java
import java.util.PriorityQueue;

public class Solution {
    public int eatenApples(int[] apples, int[] days) {
        // Heap lưu int[]{ngày_hỏng_thực_tế, số_lượng_táo}
        PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> a[0] - b[0]);
        
        int eaten = 0;
        int i = 0;
        int n = apples.length;
        
        while (i < n || !minHeap.isEmpty()) {
            if (i < n && apples[i] > 0) {
                minHeap.offer(new int[]{i + days[i], apples[i]});
            }
            
            while (!minHeap.isEmpty() && (minHeap.peek()[0] <= i || minHeap.peek()[1] == 0)) {
                minHeap.poll(); // Loại bỏ táo hỏng hoặc hết
            }
            
            if (!minHeap.isEmpty()) {
                eaten++;
                minHeap.peek()[1]--; // Ăn 1 quả
            }
            i++;
        }
        
        return eaten;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log N + D \log N)$ với $D$ là số ngày mà táo có thể lưu trữ tối đa. Số thao tác đẩy vào/lấy ra từ Heap tỷ lệ thuận với số cụm táo.
- **Space Complexity:** $O(N)$ lưu trữ các lô táo trong Heap.

---

## 25. Meeting Rooms III
### Đề bài
Cho `n` phòng họp và mảng `meetings` chứa khoảng thời gian `[start, end]`. Khi một cuộc họp bắt đầu, nó sẽ được xếp vào phòng có số ID nhỏ nhất đang trống. Nếu không có phòng nào, nó sẽ bị trì hoãn cho đến khi có phòng (thời lượng cuộc họp giữ nguyên). Trả về ID của phòng tổ chức nhiều cuộc họp nhất.

### Phân tích thuật toán
Tương tự bài toán ghế xếp, dùng **hai Min Heap**:
1. `availableRooms` lưu ID các phòng đang trống.
2. `occupiedRooms` lưu `[thời_điểm_kết_thúc, ID_phòng]` ưu tiên theo thời gian xong nhỏ nhất, sau đó ưu tiên ID phòng nhỏ nhất.
Sort mảng `meetings` theo thời gian bắt đầu. Tại mỗi cuộc họp, giải phóng tất cả các phòng đã kết thúc trước hoặc bằng thời điểm bắt đầu. 
Nếu có phòng trống, chọn phòng nhỏ nhất từ `availableRooms`. Nếu không có, lấy phòng hoàn thành sớm nhất từ `occupiedRooms`, kéo dài thời gian kết thúc của cuộc họp này cộng dồn lên. Theo dõi số lần dùng của từng ID phòng.

### Mã nguồn Java
```java
import java.util.PriorityQueue;
import java.util.Arrays;

public class Solution {
    public int mostBooked(int n, int[][] meetings) {
        Arrays.sort(meetings, (a, b) -> a[0] - b[0]);
        
        PriorityQueue<Integer> availableRooms = new PriorityQueue<>();
        for (int i = 0; i < n; i++) {
            availableRooms.offer(i);
        }
        
        // Heap lưu long[]{endTime, roomId} (Dùng long để tránh tràn số)
        PriorityQueue<long[]> occupiedRooms = new PriorityQueue<>((a, b) -> {
            if (a[0] == b[0]) return Long.compare(a[1], b[1]);
            return Long.compare(a[0], b[0]);
        });
        
        int[] meetingCount = new int[n];
        
        for (int[] meet : meetings) {
            int start = meet[0];
            int end = meet[1];
            
            while (!occupiedRooms.isEmpty() && occupiedRooms.peek()[0] <= start) {
                availableRooms.offer((int) occupiedRooms.poll()[1]);
            }
            
            if (!availableRooms.isEmpty()) {
                int room = availableRooms.poll();
                meetingCount[room]++;
                occupiedRooms.offer(new long[]{end, room});
            } else {
                long[] firstFinished = occupiedRooms.poll();
                long newEndTime = firstFinished[0] + (end - start);
                int room = (int) firstFinished[1];
                meetingCount[room]++;
                occupiedRooms.offer(new long[]{newEndTime, room});
            }
        }
        
        int maxCount = 0;
        int resultRoom = 0;
        for (int i = 0; i < n; i++) {
            if (meetingCount[i] > maxCount) {
                maxCount = meetingCount[i];
                resultRoom = i;
            }
        }
        
        return resultRoom;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(M \log M + M \log N)$ với $M$ là số cuộc họp.
- **Space Complexity:** $O(N)$ lưu `n` phòng họp trong các cấu trúc Heap.

---

## 26. Find Median from Data Stream
### Đề bài
Thiết kế cấu trúc dữ liệu cho phép thêm từng số một vào hệ thống, và có thể lấy ra giá trị trung vị (Median) của toàn bộ các số hiện tại.
Trung vị là số đứng giữa một tập hợp đã sắp xếp. Nếu tập có số phần tử chẵn, trung vị là trung bình cộng của 2 số ở giữa.

### Phân tích thuật toán
Sử dụng **hai Heap**:
- `maxHeap` chứa nửa các giá trị nhỏ hơn của dữ liệu.
- `minHeap` chứa nửa các giá trị lớn hơn của dữ liệu.
Cân bằng hai Heap này sao cho: 
- `maxHeap.size()` có thể bằng hoặc lớn hơn `minHeap.size()` đúng 1 phần tử.
- Mọi phần tử trong `maxHeap` đều nhỏ hơn hoặc bằng mọi phần tử trong `minHeap`.
Khi lấy trung vị, nếu tổng số phần tử lẻ, nó nằm ở đỉnh `maxHeap`. Nếu chẵn, nó là trung bình cộng của 2 đỉnh.

### Mã nguồn Java
```java
import java.util.PriorityQueue;
import java.util.Collections;

class MedianFinder {
    private PriorityQueue<Integer> maxHeap; // Nửa dưới
    private PriorityQueue<Integer> minHeap; // Nửa trên

    public MedianFinder() {
        maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        minHeap = new PriorityQueue<>();
    }
    
    public void addNum(int num) {
        maxHeap.offer(num);
        minHeap.offer(maxHeap.poll());
        
        // Đảm bảo maxHeap có nhiều phần tử hơn hoặc bằng minHeap
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

### Độ phức tạp
- **Time Complexity:** $O(\log N)$ cho `addNum`, $O(1)$ cho `findMedian`.
- **Space Complexity:** $O(N)$ để lưu toàn bộ dữ liệu.

---

## 27. Sliding Window Maximum
### Đề bài
Cho một mảng `nums` và một cửa sổ kích thước `k` trượt từ trái qua phải. Bạn chỉ có thể nhìn thấy `k` số trong cửa sổ. Trả về mảng chứa các giá trị lớn nhất ở mỗi cửa sổ trượt.

### Phân tích thuật toán
Sử dụng **Max Heap** lưu các phần tử dưới dạng cặp `(giá_trị, chỉ_số)`.
Khi cửa sổ dịch sang phải, ta thêm phần tử mới vào Max Heap. Đỉnh của Heap là phần tử lớn nhất, nhưng có thể nó có chỉ số nằm ngoài cửa sổ (chỉ số $\le i - k$). Nếu vậy, ta loại bỏ (pop) nó ra cho đến khi phần tử đỉnh là hợp lệ và ghi kết quả.

### Mã nguồn Java
```java
import java.util.PriorityQueue;

public class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        int[] result = new int[n - k + 1];
        
        // Heap lưu mảng int[]{giá_trị, chỉ_số}
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) -> b[0] - a[0]);
        
        for (int i = 0; i < k; i++) {
            maxHeap.offer(new int[]{nums[i], i});
        }
        
        result[0] = maxHeap.peek()[0];
        
        for (int i = k; i < n; i++) {
            maxHeap.offer(new int[]{nums[i], i});
            
            // Loại bỏ các phần tử nằm ngoài Window
            while (maxHeap.peek()[1] <= i - k) {
                maxHeap.poll();
            }
            
            result[i - k + 1] = maxHeap.peek()[0];
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log N)$ (trường hợp xấu nhất phần tử tăng dần, heap không bị thu hẹp nhiều).
- **Space Complexity:** $O(N)$ lưu `N` phần tử trong Max Heap.

---

## 28. IPO (Maximize Capital)
### Đề bài
Giả sử có `n` dự án, dự án `i` yêu cầu vốn tối thiểu `capital[i]` và sinh lời `profits[i]`. Ban đầu bạn có số vốn là `w`. Bạn có thể thực hiện tối đa `k` dự án trước khi IPO. Mỗi dự án chỉ làm 1 lần, sau khi làm vốn `w` tăng thêm `profits[i]`. Tính tổng số vốn tối đa.

### Phân tích thuật toán
Dùng **Min Heap** `minCapitalHeap` để quản lý các dự án theo số vốn `capital` tăng dần.
Dùng **Max Heap** `maxProfitHeap` để lưu `profit` của những dự án có vốn $\le w$.
Ở mỗi bước (tối đa `k` lần), đẩy tất cả dự án đủ điều kiện vốn từ `minCapitalHeap` sang `maxProfitHeap`. Sau đó lấy dự án có `profit` cao nhất để làm, cộng vào `w`.

### Mã nguồn Java
```java
import java.util.PriorityQueue;

public class Solution {
    public int findMaximizedCapital(int k, int w, int[] profits, int[] capital) {
        int n = profits.length;
        // Heap lưu int[]{capital, profit}
        PriorityQueue<int[]> minCapitalHeap = new PriorityQueue<>((a, b) -> a[0] - b[0]);
        PriorityQueue<Integer> maxProfitHeap = new PriorityQueue<>((a, b) -> b - a);
        
        for (int i = 0; i < n; i++) {
            minCapitalHeap.offer(new int[]{capital[i], profits[i]});
        }
        
        for (int i = 0; i < k; i++) {
            while (!minCapitalHeap.isEmpty() && minCapitalHeap.peek()[0] <= w) {
                maxProfitHeap.offer(minCapitalHeap.poll()[1]);
            }
            
            if (maxProfitHeap.isEmpty()) {
                break; // Không còn dự án nào đủ vốn để thực hiện
            }
            
            w += maxProfitHeap.poll();
        }
        
        return w;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log N + K \log N)$. Pop từ min heap và đẩy sang max heap mất tối đa $O(N \log N)$, sau đó k vòng lặp lấy đỉnh tốn $O(K \log N)$.
- **Space Complexity:** $O(N)$ để lưu trữ 2 cấu trúc Heap.

---

## 29. Find the Kth Largest Integer in the Array
### Đề bài
Cho một mảng chuỗi `nums`, mỗi chuỗi đại diện cho một số nguyên (có thể rất lớn) và số `k`. Hãy trả về chuỗi đại diện cho số nguyên lớn thứ `k` trong mảng.

### Phân tích thuật toán
Mặc dù là số, nhưng chúng có thể vượt quá kiểu `long` (lên đến 100 chữ số). Do đó phải so sánh dưới dạng chuỗi `String`.
Quy tắc so sánh 2 số nguyên dương dạng chuỗi: 
- Nếu chiều dài khác nhau, chuỗi dài hơn lớn hơn.
- Nếu chiều dài giống nhau, dùng `compareTo` bình thường của Java.
Sử dụng **Min Heap** cỡ `k` với Custom Comparator như trên. Cuối cùng, phần tử ở đỉnh là kết quả.

### Mã nguồn Java
```java
import java.util.PriorityQueue;

public class Solution {
    public String kthLargestNumber(String[] nums, int k) {
        PriorityQueue<String> minHeap = new PriorityQueue<>((a, b) -> {
            if (a.length() == b.length()) {
                return a.compareTo(b);
            }
            return a.length() - b.length();
        });
        
        for (String num : nums) {
            minHeap.offer(num);
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }
        
        return minHeap.peek();
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log K \cdot M)$, với $M$ là chiều dài trung bình của các chuỗi (để so sánh).
- **Space Complexity:** $O(K \cdot M)$ lưu tối đa $K$ chuỗi trong Min Heap.

---

## 30. Design Twitter
### Đề bài
Thiết kế phiên bản đơn giản của Twitter với các tính năng:
- `postTweet(userId, tweetId)`: Người dùng tạo bài viết mới.
- `getNewsFeed(userId)`: Trả về top 10 bài viết mới nhất trong luồng tin tức của người dùng (từ bản thân và người họ theo dõi).
- `follow(followerId, followeeId)`: Theo dõi một người khác.
- `unfollow(followerId, followeeId)`: Hủy theo dõi một người.

### Phân tích thuật toán
Sử dụng một bộ đếm toàn cục `timestamp` để biết thời gian tweet.
Mỗi User có một danh sách `Tweet` dạng LinkedList và tập `HashSet` chứa những người họ follow.
Khi gọi `getNewsFeed`, bài toán quay về dạng **Merge K Sorted Lists**. Lấy đỉnh của danh sách Tweet từ user hiện tại và tất cả followee, đẩy vào **Max Heap** (kích thước lấy top 10).
Hàm `poll()` 10 lần từ Heap và di chuyển `Node` theo kiểu LinkedList.

### Mã nguồn Java
```java
import java.util.List;
import java.util.ArrayList;
import java.util.Map;
import java.util.HashMap;
import java.util.Set;
import java.util.HashSet;
import java.util.PriorityQueue;

class Twitter {
    private static int timestamp = 0;
    
    private class Tweet {
        int id;
        int time;
        Tweet next;
        
        public Tweet(int id) {
            this.id = id;
            this.time = timestamp++;
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
            follow(id); // Follow chính mình
            tweetHead = null;
        }
        
        public void follow(int id) { followed.add(id); }
        public void unfollow(int id) { if(id != this.id) followed.remove(id); }
        
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
        List<Integer> result = new ArrayList<>();
        if (!userMap.containsKey(userId)) return result;
        
        Set<Integer> users = userMap.get(userId).followed;
        PriorityQueue<Tweet> maxHeap = new PriorityQueue<>((a, b) -> b.time - a.time);
        
        for (int user : users) {
            Tweet t = userMap.get(user).tweetHead;
            if (t != null) {
                maxHeap.offer(t);
            }
        }
        
        int n = 0;
        while (!maxHeap.isEmpty() && n < 10) {
            Tweet t = maxHeap.poll();
            result.add(t.id);
            n++;
            if (t.next != null) {
                maxHeap.offer(t.next);
            }
        }
        return result;
    }
    
    public void follow(int followerId, int followeeId) {
        userMap.putIfAbsent(followerId, new User(followerId));
        userMap.putIfAbsent(followeeId, new User(followeeId));
        userMap.get(followerId).follow(followeeId);
    }
    
    public void unfollow(int followerId, int followeeId) {
        if (!userMap.containsKey(followerId) || followerId == followeeId) return;
        userMap.get(followerId).unfollow(followeeId);
    }
}
```

### Độ phức tạp
- **Time Complexity:** 
  - `postTweet`, `follow`, `unfollow`: $O(1)$.
  - `getNewsFeed`: $O(F + 10 \log F)$ với $F$ là số lượng người đang follow. Thao tác rất hiệu quả.
- **Space Complexity:** $O(U + T)$ với $U$ là số users và $T$ là số tweets trong hệ thống.

