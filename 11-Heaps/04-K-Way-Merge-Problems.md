# Danh sách bài tập K-Way Merge

## 1. Merge k Sorted Lists
**Đề bài chi tiết:**
Cho mảng của `k` danh sách liên kết (linked lists), mỗi danh sách liên kết đã được sắp xếp theo thứ tự tăng dần. Hãy hợp nhất tất cả các danh sách liên kết thành một danh sách liên kết duy nhất được sắp xếp và trả về danh sách đó.

**Phân tích thuật toán:**
Dùng một Min-Heap (PriorityQueue) để lưu giữ phần tử đầu tiên của tất cả các danh sách liên kết. Heap sẽ được sắp xếp dựa trên giá trị của các node. Ở mỗi bước, lấy node nhỏ nhất khỏi heap, đưa nó vào danh sách kết quả, và nếu node đó có node tiếp theo, đưa node tiếp theo vào heap.

**Mã nguồn Java:**
```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) return null;
        PriorityQueue<ListNode> pq = new PriorityQueue<>(lists.length, (a, b) -> Integer.compare(a.val, b.val));
        
        for (ListNode node : lists) {
            if (node != null) {
                pq.add(node);
            }
        }
        
        ListNode dummy = new ListNode(0);
        ListNode tail = dummy;
        
        while (!pq.isEmpty()) {
            ListNode current = pq.poll();
            tail.next = current;
            tail = tail.next;
            
            if (current.next != null) {
                pq.add(current.next);
            }
        }
        return dummy.next;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log K)$, trong đó N là tổng số phần tử của tất cả các danh sách, K là số lượng danh sách.
- **Không gian (Space):** $O(K)$ cho bộ nhớ của Heap.

---

## 2. Kth Smallest Element in a Sorted Matrix
**Đề bài chi tiết:**
Cho ma trận `n x n` trong đó mỗi hàng và mỗi cột đều được sắp xếp theo thứ tự không giảm, tìm phần tử nhỏ thứ `k` trong ma trận. Lưu ý: đây là phần tử nhỏ thứ `k` trong thứ tự đã sắp xếp chung chứ không phải là phần tử phân biệt.

**Phân tích thuật toán:**
Mỗi hàng của ma trận là một mảng đã được sắp xếp. Đẩy phần tử đầu tiên của mỗi hàng (hoặc một số hàng đầu tiên) vào Min-Heap. Mỗi lần lấy phần tử nhỏ nhất ra khỏi heap, ta đẩy phần tử tiếp theo ở cùng hàng vào heap. Làm k lần như vậy.

**Mã nguồn Java:**
```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> Integer.compare(a[0], b[0]));
        
        for (int i = 0; i < Math.min(n, k); i++) {
            pq.offer(new int[]{matrix[i][0], i, 0});
        }
        
        int count = 0;
        int result = -1;
        
        while (!pq.isEmpty()) {
            int[] current = pq.poll();
            result = current[0];
            int row = current[1];
            int col = current[2];
            count++;
            
            if (count == k) break;
            
            if (col + 1 < n) {
                pq.offer(new int[]{matrix[row][col + 1], row, col + 1});
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(X \log X + k \log X)$ với $X = \min(N, k)$.
- **Không gian (Space):** $O(X)$ kích thước của Heap.

---

## 3. Smallest Range Covering Elements from K Lists
**Đề bài chi tiết:**
Bạn có `k` danh sách các số nguyên được sắp xếp tăng dần. Hãy tìm khoảng (range) $[a, b]$ nhỏ nhất có chứa ít nhất một phần tử từ mỗi `k` danh sách.

**Phân tích thuật toán:**
Dùng Min-Heap theo dõi phần tử nhỏ nhất và một biến max để theo dõi phần tử lớn nhất hiện tại. Khởi tạo bằng cách đưa phần tử đầu của mỗi danh sách vào Heap. Khoảng lúc này là `[Heap.peek(), max]`. Mỗi bước pop phần tử nhỏ nhất và lấy phần tử tiếp theo từ cùng mảng vào Heap, cập nhật biến max và cập nhật lại khoảng nhỏ nhất.

**Mã nguồn Java:**
```java
class Solution {
    public int[] smallestRange(List<List<Integer>> nums) {
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> Integer.compare(a[0], b[0]));
        int max = Integer.MIN_VALUE;
        
        for (int i = 0; i < nums.size(); i++) {
            int val = nums.get(i).get(0);
            pq.offer(new int[]{val, i, 0});
            max = Math.max(max, val);
        }
        
        int rangeStart = 0;
        int rangeEnd = Integer.MAX_VALUE;
        
        while (pq.size() == nums.size()) {
            int[] current = pq.poll();
            int min = current[0];
            int row = current[1];
            int col = current[2];
            
            if (max - min < rangeEnd - rangeStart) {
                rangeStart = min;
                rangeEnd = max;
            }
            
            if (col + 1 < nums.get(row).size()) {
                int nextVal = nums.get(row).get(col + 1);
                pq.offer(new int[]{nextVal, row, col + 1});
                max = Math.max(max, nextVal);
            }
        }
        return new int[]{rangeStart, rangeEnd};
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log K)$ với N tổng số phần tử.
- **Không gian (Space):** $O(K)$.

---

## 4. Find K-th Smallest Pair Distance
**Đề bài chi tiết:**
Khoảng cách của một cặp số nguyên (a, b) là giá trị tuyệt đối của hiệu giữa a và b. Cho một mảng nguyên nums và số nguyên k, trả về khoảng cách thứ k nhỏ nhất trong tất cả các cặp có thể.

**Phân tích thuật toán:**
Tuy bài này thường dùng Binary Search + Two Pointers tối ưu nhất ($O(N \log N + N \log W)$), nhưng có thể tiếp cận bằng K-Way Merge ý tưởng (mặc dù bị TLE do $O(k \log N)$ lớn). Sắp xếp mảng. Cặp tạo bởi $i$ và $j$ (với $j > i$) có thể coi là K mảng đã được sắp xếp (cố định i, tăng j).

**Mã nguồn Java:**
```java
// Cách tiếp cận dùng K-Way Merge (Có thể TLE với K lớn, minh hoạ pattern)
class Solution {
    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums);
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> Integer.compare(a[0], b[0]));
        
        for (int i = 0; i < nums.length - 1; i++) {
            pq.offer(new int[]{nums[i + 1] - nums[i], i, i + 1});
        }
        
        int result = 0;
        while (k > 0 && !pq.isEmpty()) {
            int[] current = pq.poll();
            result = current[0];
            k--;
            
            int i = current[1];
            int j = current[2];
            if (j + 1 < nums.length) {
                pq.offer(new int[]{nums[j + 1] - nums[i], i, j + 1});
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log N + K \log N)$.
- **Không gian (Space):** $O(N)$ cho Heap.

---

## 5. Kth Smallest Number in Multiplication Table
**Đề bài chi tiết:**
Bảng cửu chương có kích thước m x n chứa giá trị `i * j` tại ô `(i, j)`. Trả về số nhỏ thứ k trong bảng.

**Phân tích thuật toán:**
Giống hệt ma trận đã được sắp xếp nhưng không lưu trữ toàn bộ bảng vào bộ nhớ. Mỗi hàng $i$ là các bội số của $i$. Ta có thể đẩy $m$ phần tử đầu của mỗi hàng vào Min-Heap và lặp $k$ lần.

**Mã nguồn Java:**
```java
// Cách tiếp cận K-Way Merge (Minh họa, Binary Search sẽ tối ưu hơn)
class Solution {
    public int findKthNumber(int m, int n, int k) {
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> Integer.compare(a[0], b[0]));
        
        for (int i = 1; i <= m; i++) {
            pq.offer(new int[]{i, i, 1}); // {giá_trị, base_hàng, hệ_số_cột}
        }
        
        int count = 0;
        int ans = 0;
        while (!pq.isEmpty()) {
            int[] curr = pq.poll();
            ans = curr[0];
            count++;
            
            if (count == k) break;
            
            int row = curr[1];
            int col = curr[2];
            if (col + 1 <= n) {
                pq.offer(new int[]{row * (col + 1), row, col + 1});
            }
        }
        return ans;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(k \log m)$.
- **Không gian (Space):** $O(m)$.

---

## 6. Super Ugly Number
**Đề bài chi tiết:**
Số siêu xấu (Super Ugly Number) là số nguyên dương có tất cả các ước nguyên tố đều thuộc tập primes đã cho. Tìm số siêu xấu thứ n. Số 1 luôn là super ugly.

**Phân tích thuật toán:**
Dùng Min-Heap. Mỗi prime $p$ đại diện cho một danh sách (các phần tử sinh ra khi nhân primes vào mảng kết quả đã tìm được). Mỗi bước lấy giá trị min ra, sau đó đưa giá trị tạo bởi $prime \times uglyArray[index]$ vào.

**Mã nguồn Java:**
```java
class Solution {
    public int nthSuperUglyNumber(int n, int[] primes) {
        int[] ugly = new int[n];
        ugly[0] = 1;
        PriorityQueue<long[]> pq = new PriorityQueue<>((a, b) -> Long.compare(a[0], b[0]));
        
        for (int i = 0; i < primes.length; i++) {
            pq.offer(new long[]{primes[i], primes[i], 0}); 
            // {giá_trị, prime, chỉ_số_trong_mảng_ugly}
        }
        
        int i = 1;
        while (i < n) {
            long[] current = pq.poll();
            long val = current[0];
            long prime = current[1];
            int idx = (int) current[2];
            
            if (val != ugly[i - 1]) {
                ugly[i] = (int) val;
                i++;
            }
            pq.offer(new long[]{prime * ugly[idx + 1], prime, idx + 1});
        }
        return ugly[n - 1];
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log K)$ với K là số nguyên tố trong `primes`.
- **Không gian (Space):** $O(N + K)$.

---

## 7. Find K Pairs with Smallest Sums
**Đề bài chi tiết:**
Bạn có 2 mảng nguyên đã được sắp xếp `nums1` và `nums2` và một số nguyên k. Định nghĩa cặp (u, v) trong đó u từ `nums1`, v từ `nums2`. Tìm K cặp có tổng $u + v$ nhỏ nhất.

**Phân tích thuật toán:**
Dùng Min-Heap. Đẩy mảng các phần tử `(nums1[i], nums2[0], chỉ số của phần tử trong nums2)` vào. Sau khi lấy một cặp có tổng nhỏ nhất, nếu có phần tử kế tiếp trong `nums2`, thêm cặp mới vào heap.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums1.length == 0 || nums2.length == 0 || k == 0) return res;
        
        PriorityQueue<int[]> pq = new PriorityQueue<>(
            (a, b) -> (a[0] + a[1]) - (b[0] + b[1])
        );
        
        for (int i = 0; i < nums1.length && i < k; i++) {
            pq.offer(new int[]{nums1[i], nums2[0], 0});
        }
        
        while (k-- > 0 && !pq.isEmpty()) {
            int[] current = pq.poll();
            res.add(Arrays.asList(current[0], current[1]));
            
            int idxInNums2 = current[2];
            if (idxInNums2 + 1 < nums2.length) {
                pq.offer(new int[]{current[0], nums2[idxInNums2 + 1], idxInNums2 + 1});
            }
        }
        
        return res;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(k \log (\min(N, k)))$.
- **Không gian (Space):** $O(\min(N, k))$ (kích thước Heap).

---

## 8. Merge K Sorted Arrays
**Đề bài chi tiết:**
Cho K mảng 1 chiều, mỗi mảng đã được sắp xếp tăng dần. Cần gộp tất cả K mảng lại thành một mảng duy nhất cũng sắp xếp tăng dần. (Dạng gốc của Pattern).

**Phân tích thuật toán:**
Tương tự Merge K Sorted Lists, tạo class Node chứa value, arrayIndex, elementIndex. Đẩy phần tử đầu mỗi mảng vào Heap, lấy min, đưa vào kết quả, sau đó đưa tiếp array[arrayIndex][elementIndex+1] vào.

**Mã nguồn Java:**
```java
class Node implements Comparable<Node> {
    int val, arrIdx, elemIdx;
    public Node(int v, int a, int e) { val = v; arrIdx = a; elemIdx = e; }
    public int compareTo(Node o) { return Integer.compare(this.val, o.val); }
}

class Solution {
    public int[] mergeKSortedArrays(int[][] arrays) {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        int totalLen = 0;
        
        for (int i = 0; i < arrays.length; i++) {
            if (arrays[i].length > 0) {
                pq.add(new Node(arrays[i][0], i, 0));
                totalLen += arrays[i].length;
            }
        }
        
        int[] result = new int[totalLen];
        int i = 0;
        while (!pq.isEmpty()) {
            Node curr = pq.poll();
            result[i++] = curr.val;
            
            if (curr.elemIdx + 1 < arrays[curr.arrIdx].length) {
                pq.add(new Node(arrays[curr.arrIdx][curr.elemIdx + 1], curr.arrIdx, curr.elemIdx + 1));
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log K)$ với N tổng số phần tử.
- **Không gian (Space):** $O(K)$ cho Heap.

---

## 9. Ugly Number II
**Đề bài chi tiết:**
Viết chương trình tìm số Ugly number thứ n. Ugly numbers là số nguyên dương mà ước nguyên tố chỉ thuộc {2, 3, 5}.

**Phân tích thuật toán:**
Bài này là trường hợp đặc biệt của Super Ugly Number (bài 6) với tập primes = {2, 3, 5}. Giải bằng K-Way Merge sử dụng 3 con trỏ đại diện cho 3 danh sách luồng.

**Mã nguồn Java:**
```java
class Solution {
    public int nthUglyNumber(int n) {
        int[] ugly = new int[n];
        ugly[0] = 1;
        int i2 = 0, i3 = 0, i5 = 0;
        
        for (int i = 1; i < n; i++) {
            int next2 = ugly[i2] * 2;
            int next3 = ugly[i3] * 3;
            int next5 = ugly[i5] * 5;
            
            int nextMin = Math.min(next2, Math.min(next3, next5));
            ugly[i] = nextMin;
            
            if (nextMin == next2) i2++;
            if (nextMin == next3) i3++;
            if (nextMin == next5) i5++;
        }
        return ugly[n - 1];
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N)$.
- **Không gian (Space):** $O(N)$.

---

## 10. Sort a nearly sorted (or K sorted) array
**Đề bài chi tiết:**
Cho một mảng $n$ phần tử, trong đó mỗi phần tử nằm cách vị trí đúng của nó sau khi sắp xếp tối đa $k$ vị trí. Hãy sắp xếp mảng này thật nhanh.

**Phân tích thuật toán:**
Dùng Min-Heap kích thước $k+1$. Thêm $k+1$ phần tử đầu tiên vào heap. Tại mỗi bước, lấy phần tử nhỏ nhất đưa vào mảng kết quả, sau đó đưa phần tử kế tiếp của mảng vào heap. Do phần tử x lệch không quá k, phần tử nhỏ nhất chắc chắn luôn nằm ở top của heap.

**Mã nguồn Java:**
```java
class Solution {
    public void sortKSortedArray(int[] arr, int k) {
        if (arr == null || arr.length <= 1) return;
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        
        int n = arr.length;
        for (int i = 0; i < Math.min(n, k + 1); i++) {
            pq.add(arr[i]);
        }
        
        int index = 0;
        for (int i = k + 1; i < n; i++) {
            arr[index++] = pq.poll();
            pq.add(arr[i]);
        }
        
        while (!pq.isEmpty()) {
            arr[index++] = pq.poll();
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log K)$.
- **Không gian (Space):** $O(K)$.

---

## 11. Merge K Sorted Iterator Streams
**Đề bài chi tiết:**
Thiết kế một iterator để hợp nhất `k` luồng dữ liệu (streams) đã được sắp xếp tăng dần. Mỗi stream được biểu diễn bằng một Iterator trong Java. Yêu cầu tải dữ liệu mượt mà mà không nạp toàn bộ vào RAM.

**Phân tích thuật toán:**
Sử dụng Min-Heap (PriorityQueue) để lưu trữ các Iterator. Cần tạo một lớp bọc (wrapper) lưu giá trị hiện tại và Iterator đó để sắp xếp trong Heap. Mỗi lần gọi, lấy phần tử nhỏ nhất ra khỏi Heap và nếu Iterator đó còn phần tử tiếp theo, đẩy phần tử tiếp theo vào Heap.

**Mã nguồn Java:**
```java
import java.util.*;

class SortedIterator implements Comparable<SortedIterator> {
    Iterator<Integer> iterator;
    Integer current;

    public SortedIterator(Iterator<Integer> iterator) {
        this.iterator = iterator;
        if (iterator.hasNext()) {
            this.current = iterator.next();
        } else {
            this.current = null;
        }
    }

    @Override
    public int compareTo(SortedIterator other) {
        return this.current.compareTo(other.current);
    }
}

class Solution {
    public Iterable<Integer> mergeKIterators(List<Iterator<Integer>> iterators) {
        List<Integer> result = new ArrayList<>();
        PriorityQueue<SortedIterator> pq = new PriorityQueue<>();

        for (Iterator<Integer> it : iterators) {
            if (it.hasNext()) {
                pq.offer(new SortedIterator(it));
            }
        }

        while (!pq.isEmpty()) {
            SortedIterator curr = pq.poll();
            result.add(curr.current);
            if (curr.iterator.hasNext()) {
                curr.current = curr.iterator.next();
                pq.offer(curr);
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log K)$ với N là tổng số phần tử từ tất cả các streams.
- **Không gian (Space):** $O(K)$ cho kích thước của Min-Heap.

---

## 12. K-th Smallest in Lexicographical Order
**Đề bài chi tiết:**
Cho hai số nguyên `n` và `k`, trả về số nhỏ thứ `k` trong thứ tự từ điển (lexicographical order) trong các số từ `1` đến `n`.

**Phân tích thuật toán:**
Bài toán tìm kiếm theo thứ tự từ điển có thể hiểu như duyệt trên cây prefix (Trie) 10 phân (1-9 ở root, rồi 0-9 ở lá). Để tối ưu, ta không duyệt từng số mà đếm xem dưới mỗi nhánh (như prefix `1`, `10`...) có bao nhiêu số hợp lệ $\le n$. Nếu số lượng nhỏ hơn hoặc bằng `k`, ta bỏ qua nhánh đó và dịch sang nhánh kế tiếp, ngược lại ta đi sâu xuống nhánh đó.

**Mã nguồn Java:**
```java
class Solution {
    public int findKthNumber(int n, int k) {
        long curr = 1;
        k--; // Đã tính phần tử 1 là giá trị hiện tại
        
        while (k > 0) {
            long steps = countSteps(n, curr, curr + 1);
            if (steps <= k) {
                curr += 1;
                k -= steps; // Bỏ qua toàn bộ cây con bên dưới curr
            } else {
                curr *= 10;
                k -= 1; // Đi sâu xuống một tầng
            }
        }
        return (int) curr;
    }
    
    private long countSteps(long n, long prefix1, long prefix2) {
        long steps = 0;
        while (prefix1 <= n) {
            steps += Math.min(n + 1, prefix2) - prefix1;
            prefix1 *= 10;
            prefix2 *= 10;
        }
        return steps;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O((\log_{10} n)^2)$. Đếm steps mất $O(\log_{10} n)$ và duyệt cây cũng tốn $O(\log_{10} n)$ bước.
- **Không gian (Space):** $O(1)$.

---

## 13. Find K-th Largest XOR Coordinate Value
**Đề bài chi tiết:**
Cho ma trận 2D `matrix`, giá trị tọa độ `(i, j)` là phép XOR của tất cả phần tử `matrix[a][b]` với `0 <= a <= i` và `0 <= b <= j`. Tìm giá trị lớn thứ `k` trong tất cả các tọa độ.

**Phân tích thuật toán:**
Sử dụng mảng cộng dồn 2D cho phép XOR. Mảng lưu trữ kết quả XOR đến `(i, j)`. Cùng lúc đó, duy trì một Min-Heap kích thước $K$ để theo dõi $K$ phần tử lớn nhất. Khi duyệt xong toàn bộ ma trận, đỉnh của Min-Heap sẽ là phần tử lớn thứ $K$.

**Mã nguồn Java:**
```java
class Solution {
    public int kthLargestValue(int[][] matrix, int k) {
        int m = matrix.length;
        int n = matrix[0].length;
        int[][] prefixXor = new int[m + 1][n + 1];
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                prefixXor[i][j] = prefixXor[i - 1][j] ^ prefixXor[i][j - 1] 
                                ^ prefixXor[i - 1][j - 1] ^ matrix[i - 1][j - 1];
                
                pq.offer(prefixXor[i][j]);
                if (pq.size() > k) {
                    pq.poll();
                }
            }
        }
        return pq.peek();
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(M \cdot N \cdot \log K)$.
- **Không gian (Space):** $O(M \cdot N + K)$.

---

## 14. Kth Largest Element in a Stream
**Đề bài chi tiết:**
Thiết kế một lớp để tìm phần tử lớn thứ `k` trong một luồng dữ liệu (stream). Lớp phải khởi tạo với kích thước `k` và một mảng ban đầu, có phương thức `add(int val)` để thêm giá trị mới vào luồng và trả về phần tử lớn thứ `k`.

**Phân tích thuật toán:**
Dùng một Min-Heap có kích thước giới hạn là `k`. Mỗi khi thêm phần tử mới, đẩy nó vào Heap. Nếu số lượng phần tử vượt quá `k`, thì lấy phần tử nhỏ nhất ra. Do Heap chỉ chứa tối đa `k` phần tử, đỉnh của Heap luôn là phần tử lớn thứ `k` trong toàn bộ luồng.

**Mã nguồn Java:**
```java
class KthLargest {
    PriorityQueue<Integer> pq;
    int k;

    public KthLargest(int k, int[] nums) {
        this.k = k;
        pq = new PriorityQueue<>();
        for (int num : nums) {
            add(num);
        }
    }
    
    public int add(int val) {
        pq.offer(val);
        if (pq.size() > k) {
            pq.poll();
        }
        return pq.peek();
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log K)$ cho khởi tạo, $O(\log K)$ cho thao tác `add`.
- **Không gian (Space):** $O(K)$.

---

## 15. Diagonal Traverse II
**Đề bài chi tiết:**
Cho một danh sách 2D `nums`, trả về tất cả các phần tử của `nums` theo thứ tự duyệt theo đường chéo từ dưới lên trên, từ trái qua phải.

**Phân tích thuật toán:**
Trên cùng một đường chéo (theo hướng yêu cầu), tổng chỉ số hàng và cột `row + col` luôn bằng nhau. Ta nhóm các phần tử có chung khóa `row + col` vào từng danh sách riêng biệt. Để thu được kết quả từ dưới lên trên, chỉ cần duyệt mỗi danh sách đường chéo ngược lại (từ cuối lên đầu). 

**Mã nguồn Java:**
```java
class Solution {
    public int[] findDiagonalOrder(List<List<Integer>> nums) {
        List<List<Integer>> diagonals = new ArrayList<>();
        int count = 0;
        
        for (int i = 0; i < nums.size(); i++) {
            for (int j = 0; j < nums.get(i).size(); j++) {
                if (diagonals.size() <= i + j) {
                    diagonals.add(new ArrayList<>());
                }
                diagonals.get(i + j).add(nums.get(i).get(j));
                count++;
            }
        }
        
        int[] result = new int[count];
        int idx = 0;
        for (List<Integer> diag : diagonals) {
            for (int i = diag.size() - 1; i >= 0; i--) {
                result[idx++] = diag.get(i);
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N)$ với N là tổng số phần tử.
- **Không gian (Space):** $O(N)$ để chứa kết quả trung gian.

---

## 16. Sort Bitonic Array
**Đề bài chi tiết:**
Một mảng được gọi là Bitonic nếu nó tăng dần rồi giảm dần. Cho một mảng Bitonic, hãy sắp xếp mảng này tăng dần.

**Phân tích thuật toán:**
Tìm đỉnh (peak) của mảng (điểm phần tử lớn nhất). Từ đỉnh này mảng bị chia làm 2 nửa: nửa đầu sắp xếp tăng dần và nửa sau sắp xếp giảm dần. Dùng phương pháp hợp nhất 2 mảng (2-way merge): duyệt xuôi nửa đầu và duyệt ngược nửa sau để gộp thành mảng tăng dần.

**Mã nguồn Java:**
```java
class Solution {
    public void sortBitonicArray(int[] arr) {
        int n = arr.length;
        if (n <= 1) return;
        
        int peak = 0;
        for (int i = 0; i < n - 1; i++) {
            if (arr[i] > arr[i + 1]) {
                peak = i;
                break;
            }
        }
        
        int[] result = new int[n];
        int i = 0, j = n - 1, k = 0;
        
        while (i <= peak && j > peak) {
            if (arr[i] < arr[j]) {
                result[k++] = arr[i++];
            } else {
                result[k++] = arr[j--];
            }
        }
        
        while (i <= peak) result[k++] = arr[i++];
        while (j > peak) result[k++] = arr[j--];
        
        System.arraycopy(result, 0, arr, 0, n);
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N)$ để hợp nhất 2 mảng.
- **Không gian (Space):** $O(N)$ tạo thêm mảng phụ lưu kết quả.

---

## 17. Merge Two Sorted Lists
**Đề bài chi tiết:**
Hợp nhất hai danh sách liên kết đã sắp xếp thành một danh sách liên kết duy nhất được sắp xếp. Đây là bài toán gốc cốt lõi nhất của K-Way Merge.

**Phân tích thuật toán:**
Vì 2 danh sách đã được sắp xếp, tạo một node giả (dummy node) và hai con trỏ. So sánh phần tử hiện tại của 2 con trỏ, nối phần tử nhỏ hơn vào danh sách kết quả, dời con trỏ đó lên. Khi 1 danh sách kết thúc, phần dư của danh sách còn lại có thể nối thẳng vào kết quả.

**Mã nguồn Java:**
```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(-1);
        ListNode current = dummy;
        
        while (l1 != null && l2 != null) {
            if (l1.val <= l2.val) {
                current.next = l1;
                l1 = l1.next;
            } else {
                current.next = l2;
                l2 = l2.next;
            }
            current = current.next;
        }
        
        if (l1 != null) current.next = l1;
        if (l2 != null) current.next = l2;
        
        return dummy.next;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N + M)$.
- **Không gian (Space):** $O(1)$.

---

## 18. Median of Two Sorted Arrays
**Đề bài chi tiết:**
Cho hai mảng đã sắp xếp `nums1` và `nums2` có kích thước lần lượt là `m` và `n`. Tìm trung vị (median) của hai mảng đã sắp xếp này với độ phức tạp thời gian $O(\log (m+n))$.

**Phân tích thuật toán:**
Dù có thể dùng ý tưởng K-Way merge đến giữa mảng, để đạt độ phức tạp logarit ta phải dùng Binary Search. Ta coi bài toán như là chia hai mảng sao cho tổng phần tử bên trái bằng tổng bên phải và phần tử lớn nhất nửa trái $\le$ phần tử nhỏ nhất nửa phải.

**Mã nguồn Java:**
```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        // Đảm bảo thao tác Binary Search trên mảng nhỏ hơn
        if (nums1.length > nums2.length) {
            return findMedianSortedArrays(nums2, nums1);
        }
        
        int m = nums1.length;
        int n = nums2.length;
        int low = 0, high = m;
        
        while (low <= high) {
            int partitionX = low + (high - low) / 2;
            int partitionY = (m + n + 1) / 2 - partitionX;
            
            int maxX = (partitionX == 0) ? Integer.MIN_VALUE : nums1[partitionX - 1];
            int minX = (partitionX == m) ? Integer.MAX_VALUE : nums1[partitionX];
            
            int maxY = (partitionY == 0) ? Integer.MIN_VALUE : nums2[partitionY - 1];
            int minY = (partitionY == n) ? Integer.MAX_VALUE : nums2[partitionY];
            
            if (maxX <= minY && maxY <= minX) {
                if ((m + n) % 2 == 0) {
                    return (Math.max(maxX, maxY) + Math.min(minX, minY)) / 2.0;
                } else {
                    return Math.max(maxX, maxY);
                }
            } else if (maxX > minY) {
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
- **Thời gian (Time):** $O(\log(\min(M, N)))$.
- **Không gian (Space):** $O(1)$.

---

## 19. Minimum Cost to Hire K Workers
**Đề bài chi tiết:**
Có `n` công nhân, mỗi người có một `quality` và `wage` tối thiểu muốn nhận. Bạn phải thuê đúng `k` công nhân, tất cả đều hưởng lương cùng một tỷ lệ $wage/quality$ và ít nhất bằng wage mong muốn của họ. Trả về tổng chi phí tối thiểu.

**Phân tích thuật toán:**
Khi chọn một nhóm $K$ công nhân, tỷ lệ chung sẽ bằng giá trị $\max(wage_i/quality_i)$ của cả nhóm. Do đó, ta sắp xếp các công nhân theo $wage/quality$. Duyệt từng công nhân (người hiện tại đang có ratio cao nhất nhóm), muốn tổng lương thấp thì tổng quality phải nhỏ. Duy trì một Max-Heap chứa $K$ quality nhỏ nhất đã gặp.

**Mã nguồn Java:**
```java
class Worker {
    int quality;
    int wage;
    double ratio;
    
    public Worker(int quality, int wage) {
        this.quality = quality;
        this.wage = wage;
        this.ratio = (double) wage / quality;
    }
}

class Solution {
    public double mincostToHireWorkers(int[] quality, int[] wage, int k) {
        int n = quality.length;
        Worker[] workers = new Worker[n];
        for (int i = 0; i < n; i++) {
            workers[i] = new Worker(quality[i], wage[i]);
        }
        
        Arrays.sort(workers, (a, b) -> Double.compare(a.ratio, b.ratio));
        
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> Integer.compare(b, a));
        int sumQuality = 0;
        double minCost = Double.MAX_VALUE;
        
        for (Worker worker : workers) {
            maxHeap.offer(worker.quality);
            sumQuality += worker.quality;
            
            if (maxHeap.size() > k) {
                sumQuality -= maxHeap.poll(); // Loại người có quality lớn nhất
            }
            
            if (maxHeap.size() == k) {
                minCost = Math.min(minCost, sumQuality * worker.ratio);
            }
        }
        return minCost;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log N + N \log K)$.
- **Không gian (Space):** $O(N + K)$.

---

## 20. Employee Free Time (Merge Intervals từ K Lists)
**Đề bài chi tiết:**
Bạn có lịch làm việc không chồng chéo của $K$ nhân viên. Mỗi phần tử là danh sách các intervals (khoảng thời gian) rảnh rỗi hoặc bận rộn. Tìm tất cả các khoảng thời gian rảnh chung (Free Time) của tất cả nhân viên.

**Phân tích thuật toán:**
Dùng Min-Heap (K-Way Merge) để trộn các danh sách khoảng thời gian từ các nhân viên. Đẩy interval đầu tiên của mỗi người vào Heap. Lấy interval có thời gian bắt đầu nhỏ nhất ra, so sánh nó với `prevEnd` đã gặp. Nếu interval hiện tại bắt đầu sau `prevEnd`, ta tìm được một khoảng rảnh. Cập nhật lại `prevEnd` = $\max(prevEnd, interval.end)$. Sau đó đẩy interval tiếp theo của cùng nhân viên vào Heap.

**Mã nguồn Java:**
```java
class Interval {
    public int start;
    public int end;
    public Interval(int _start, int _end) {
        start = _start;
        end = _end;
    }
}

class Solution {
    public List<Interval> employeeFreeTime(List<List<Interval>> schedule) {
        // {employee_index, interval_index}
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> {
            return Integer.compare(schedule.get(a[0]).get(a[1]).start, 
                                   schedule.get(b[0]).get(b[1]).start);
        });
        
        for (int i = 0; i < schedule.size(); i++) {
            if (schedule.get(i).size() > 0) {
                pq.offer(new int[]{i, 0});
            }
        }
        
        List<Interval> result = new ArrayList<>();
        if(pq.isEmpty()) return result;
        
        int prevEnd = schedule.get(pq.peek()[0]).get(pq.peek()[1]).end; // Bắt đầu với End
        
        while (!pq.isEmpty()) {
            int[] current = pq.poll();
            int empIdx = current[0];
            int intIdx = current[1];
            Interval interval = schedule.get(empIdx).get(intIdx);
            
            if (interval.start > prevEnd) {
                result.add(new Interval(prevEnd, interval.start));
            }
            prevEnd = Math.max(prevEnd, interval.end);
            
            if (intIdx + 1 < schedule.get(empIdx).size()) {
                pq.offer(new int[]{empIdx, intIdx + 1});
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log K)$ với N là tổng số intervals, K là số nhân viên.
- **Không gian (Space):** $O(K)$ cho Heap kích thước tối đa là số luồng nhân viên.

---

## 21. Multi-Dimensional Matrix Search (K-th Smallest in 3D/N-D Matrix)
**Đề bài chi tiết:**
Mở rộng từ bài toán tìm phần tử nhỏ thứ K trong ma trận 2D, giả sử bạn có một ma trận 3D (hoặc nhiều mảng 2D), trong đó mỗi hàng của các mặt cắt đều được sắp xếp tăng dần. Tìm phần tử nhỏ thứ `k` trong toàn bộ dữ liệu này.

**Phân tích thuật toán:**
Cốt lõi của K-Way Merge không phụ thuộc vào số chiều của dữ liệu, mà phụ thuộc vào số lượng "luồng" (streams) đã được sắp xếp. Trong ma trận 3D, ta có thể coi mỗi hàng 1D của nó là một danh sách đã được sắp xếp. Đưa phần tử đầu tiên của tất cả các hàng này vào một Min-Heap. Mỗi lần lấy phần tử nhỏ nhất ra, ta đẩy phần tử kế tiếp của cùng hàng đó vào Heap.

**Mã nguồn Java:**
```java
class Solution {
    public int kthSmallest3D(int[][][] matrix, int k) {
        // Mảng phần tử: {giá_trị, chiều_sâu, hàng, cột}
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> Integer.compare(a[0], b[0]));
        
        int depth = matrix.length;
        int rows = matrix[0].length;
        
        for (int d = 0; d < depth; d++) {
            for (int r = 0; r < rows; r++) {
                if (matrix[d][r].length > 0) {
                    pq.offer(new int[]{matrix[d][r][0], d, r, 0});
                }
            }
        }
        
        int count = 0;
        int result = -1;
        while (!pq.isEmpty()) {
            int[] curr = pq.poll();
            result = curr[0];
            count++;
            
            if (count == k) return result;
            
            int d = curr[1], r = curr[2], c = curr[3];
            if (c + 1 < matrix[d][r].length) {
                pq.offer(new int[]{matrix[d][r][c + 1], d, r, c + 1});
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(S \log S + k \log S)$ với $S$ là tổng số hàng (streams).
- **Không gian (Space):** $O(S)$ để duy trì Heap.

---

## 22. K Pairs with Maximum Sums
**Đề bài chi tiết:**
Cho hai mảng số nguyên `nums1` và `nums2` đều đã được sắp xếp tăng dần, và số nguyên `k`. Tìm `k` cặp phần tử $(u, v)$ (với $u \in nums1$ và $v \in nums2$) sao cho tổng $u + v$ là lớn nhất.

**Phân tích thuật toán:**
Do mảng sắp xếp tăng dần, các phần tử lớn nhất nằm ở cuối mảng. Tổng lớn nhất chắc chắn là `nums1[m-1] + nums2[n-1]`. Dùng Max-Heap thay vì Min-Heap. Bắt đầu đẩy phần tử `(nums1[m-1], nums2[n-1])` vào Heap. Mỗi khi lấy ra cặp lớn nhất, ta thay thế phần tử của `nums2` bằng phần tử đứng trước nó trong `nums2` (có thể kết hợp di chuyển pointer của `nums1` hoặc đẩy thêm tổ hợp tùy hướng đi, ở đây ta cố định 1 hướng và dùng Max-Heap tương tự Min-Heap ngược).

**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> kLargestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums1.length == 0 || nums2.length == 0 || k == 0) return res;
        
        int m = nums1.length, n = nums2.length;
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare(nums1[b[0]] + nums2[b[1]], nums1[a[0]] + nums2[a[1]])
        );
        
        for (int i = 0; i < m && i < k; i++) {
            maxHeap.offer(new int[]{i, n - 1});
        }
        
        while (k-- > 0 && !maxHeap.isEmpty()) {
            int[] curr = maxHeap.poll();
            res.add(Arrays.asList(nums1[curr[0]], nums2[curr[1]]));
            
            if (curr[1] - 1 >= 0) {
                maxHeap.offer(new int[]{curr[0], curr[1] - 1});
            }
        }
        
        return res;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(k \log (\min(M, k)))$.
- **Không gian (Space):** $O(\min(M, k))$.

---

## 23. K-th Smallest Prime Fraction
**Đề bài chi tiết:**
Cho một mảng `arr` đã sắp xếp tăng dần chứa số 1 và các số nguyên tố. Phân số được tạo bằng `arr[i] / arr[j]` với $i < j$. Trả về phân số nhỏ thứ `k` dưới dạng mảng 2 phần tử `[arr[i], arr[j]]`.

**Phân tích thuật toán:**
Vì mảng tăng dần, với mỗi tử số `arr[i]`, mẫu số lớn nhất là `arr[n-1]` sẽ tạo ra phân số nhỏ nhất $\frac{arr[i]}{arr[n-1]}$. Ta đẩy tất cả $\frac{arr[i]}{arr[n-1]}$ vào Min-Heap. Khi lấy phân số nhỏ nhất ra (có mẫu số ở vị trí $j$), phân số nhỏ tiếp theo cùng tử số sẽ là $\frac{arr[i]}{arr[j-1]}$. 

**Mã nguồn Java:**
```java
class Solution {
    public int[] kthSmallestPrimeFraction(int[] arr, int k) {
        int n = arr.length;
        // Heap lưu {chỉ số tử số, chỉ số mẫu số}
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> {
            // So sánh arr[a[0]]/arr[a[1]] với arr[b[0]]/arr[b[1]]
            // Tương đương so sánh: arr[a[0]] * arr[b[1]] với arr[b[0]] * arr[a[1]]
            return Integer.compare(arr[a[0]] * arr[b[1]], arr[b[0]] * arr[a[1]]);
        });
        
        for (int i = 0; i < n - 1; i++) {
            pq.offer(new int[]{i, n - 1});
        }
        
        while (--k > 0) {
            int[] curr = pq.poll();
            int i = curr[0];
            int j = curr[1];
            
            if (j - 1 > i) {
                pq.offer(new int[]{i, j - 1});
            }
        }
        
        int[] result = pq.poll();
        return new int[]{arr[result[0]], arr[result[1]]};
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log N + k \log N)$.
- **Không gian (Space):** $O(N)$ lưu trữ trong Heap.

---

## 24. K-th Element of K sorted Arrays
**Đề bài chi tiết:**
Bạn có `K` mảng đã được sắp xếp tăng dần, độ dài các mảng có thể khác nhau. Tìm phần tử nhỏ thứ `N` (K-th element) trong tất cả các mảng gộp lại.

**Phân tích thuật toán:**
Đây là dạng bài cơ bản nhất của Pattern K-Way Merge. Đưa phần tử đầu tiên của mỗi mảng vào một Min-Heap. Mỗi lần poll phần tử nhỏ nhất, ta đếm lên 1. Sau đó kiểm tra mảng chứa phần tử vừa poll có còn phần tử nào không, nếu còn thì push vào Heap. Lặp lại cho đến khi bộ đếm bằng `N`.

**Mã nguồn Java:**
```java
class Solution {
    public int findKthElement(List<List<Integer>> arrays, int k) {
        // {giá trị, chỉ số mảng, vị trí trong mảng}
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> Integer.compare(a[0], b[0]));
        
        for (int i = 0; i < arrays.size(); i++) {
            if (arrays.get(i).size() > 0) {
                pq.offer(new int[]{arrays.get(i).get(0), i, 0});
            }
        }
        
        int count = 0;
        int result = -1;
        while (!pq.isEmpty()) {
            int[] curr = pq.poll();
            result = curr[0];
            count++;
            
            if (count == k) return result;
            
            int arrIdx = curr[1];
            int elemIdx = curr[2];
            
            if (elemIdx + 1 < arrays.get(arrIdx).size()) {
                pq.offer(new int[]{arrays.get(arrIdx).get(elemIdx + 1), arrIdx, elemIdx + 1});
            }
        }
        return -1; // Không đủ K phần tử
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(M \log M + k \log M)$ với $M$ là số lượng mảng.
- **Không gian (Space):** $O(M)$ số lượng mảng trong Heap.

---

## 25. Minimum Number of Refueling Stops
**Đề bài chi tiết:**
Một chiếc xe chạy từ vị trí 0 đến `target` với `startFuel`. Trên đường có các trạm xăng `stations[i] = [position, fuel]`. Mỗi km tiêu thụ 1 đơn vị xăng. Tìm số lần đổ xăng ít nhất để đến đích.

**Phân tích thuật toán:**
Bài toán này dùng Max-Heap. Thay vì quyết định đổ xăng ngay tại trạm, ta cứ đi qua trạm và lưu trữ lượng xăng của trạm đó vào Max-Heap (như là một khoản dự trữ). Khi hết xăng (`currFuel < position` hoặc chưa tới đích), ta lấy lượng xăng lớn nhất từ Max-Heap để bổ sung. Nếu Heap rỗng mà vẫn âm xăng, nghĩa là không thể tới đích. 

**Mã nguồn Java:**
```java
class Solution {
    public int minRefuelStops(int target, int startFuel, int[][] stations) {
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        int stops = 0;
        int currFuel = startFuel;
        int i = 0;
        
        while (currFuel < target) {
            // Đưa tất cả các trạm có thể đến được với lượng xăng hiện tại vào Heap
            while (i < stations.length && stations[i][0] <= currFuel) {
                maxHeap.offer(stations[i][1]);
                i++;
            }
            
            // Nếu không thể đi tiếp và không có trạm nào để đổ thêm
            if (maxHeap.isEmpty()) {
                return -1;
            }
            
            // Đổ xăng từ trạm có nhiều xăng nhất đã đi qua
            currFuel += maxHeap.poll();
            stops++;
        }
        
        return stops;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log N)$ với $N$ là số trạm xăng.
- **Không gian (Space):** $O(N)$ cho Max-Heap.

---

## 26. K-th Smallest from a Data Stream
**Đề bài chi tiết:**
Thiết kế một cấu trúc dữ liệu cho phép nhận liên tục các số nguyên từ một luồng (stream) và luôn có thể truy vấn số nhỏ thứ `k` đã xuất hiện. (Tương tự bài K-th Largest, nhưng lấy K-th Smallest).

**Phân tích thuật toán:**
Để tìm phần tử lớn thứ K, ta dùng Min-Heap. Nhưng để tìm phần tử **nhỏ thứ K**, ta phải dùng **Max-Heap** có kích thước giới hạn là `k`. Khi thêm phần tử, nếu Max-Heap có size `< k`, cứ thêm vào. Nếu đã đủ `k`, so sánh phần tử mới với đỉnh Heap (phần tử lớn nhất trong k phần tử nhỏ nhất). Nếu nhỏ hơn đỉnh, loại đỉnh ra và đưa nó vào.

**Mã nguồn Java:**
```java
class KthSmallest {
    PriorityQueue<Integer> maxHeap;
    int k;

    public KthSmallest(int k) {
        this.k = k;
        this.maxHeap = new PriorityQueue<>(Collections.reverseOrder());
    }
    
    public void add(int val) {
        if (maxHeap.size() < k) {
            maxHeap.offer(val);
        } else if (val < maxHeap.peek()) {
            maxHeap.poll();
            maxHeap.offer(val);
        }
    }
    
    public int getKthSmallest() {
        if (maxHeap.size() < k) throw new IllegalStateException();
        return maxHeap.peek();
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(\log K)$ cho mỗi thao tác thêm mới.
- **Không gian (Space):** $O(K)$.

---

## 27. Scheduling Tasks in K CPU (Meeting Rooms II)
**Đề bài chi tiết:**
Cho một mảng các khoảng thời gian cuộc họp `intervals` gồm `start` và `end`. Tìm số lượng phòng họp tối thiểu cần thiết để tổ chức tất cả cuộc họp (tương đương với số CPU ít nhất để chạy song song các task).

**Phân tích thuật toán:**
Sắp xếp các cuộc họp theo thời gian bắt đầu. Sử dụng một Min-Heap để theo dõi thời gian kết thúc của các cuộc họp đang diễn ra. Mỗi khi có một cuộc họp mới, kiểm tra xem đỉnh của Min-Heap (cuộc họp kết thúc sớm nhất) có nhỏ hơn hoặc bằng thời gian bắt đầu của cuộc họp mới không. Nếu có, ta có thể tái sử dụng phòng đó (poll đỉnh ra). Sau đó đẩy cuộc họp mới vào.

**Mã nguồn Java:**
```java
class Solution {
    public int minMeetingRooms(int[][] intervals) {
        if (intervals == null || intervals.length == 0) return 0;
        
        // Sắp xếp cuộc họp theo thời gian bắt đầu
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
        
        // Min-Heap lưu thời gian kết thúc
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        
        minHeap.offer(intervals[0][1]);
        
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] >= minHeap.peek()) {
                // Tái sử dụng phòng họp
                minHeap.poll();
            }
            minHeap.offer(intervals[i][1]);
        }
        
        // Kích thước heap là số phòng tối thiểu
        return minHeap.size();
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log N)$ cho thao tác sắp xếp và duyệt qua Heap.
- **Không gian (Space):** $O(N)$ lưu trữ trong Heap.

---

## 28. Connect N Ropes with Minimum Cost
**Đề bài chi tiết:**
Cho mảng chiều dài của `N` sợi dây. Cần nối chúng lại thành một sợi duy nhất. Chi phí nối hai sợi dây bằng tổng chiều dài của chúng. Tìm tổng chi phí nhỏ nhất để nối tất cả.

**Phân tích thuật toán:**
Để chi phí nhỏ nhất, tại mỗi bước ta luôn phải nối 2 sợi dây ngắn nhất. Đưa tất cả vào Min-Heap. Mỗi lần lấy 2 phần tử nhỏ nhất ra, tính tổng, cộng tổng đó vào biến kết quả, sau đó đưa tổng trở lại Heap. Lặp lại cho đến khi Heap chỉ còn 1 phần tử.

**Mã nguồn Java:**
```java
class Solution {
    public int minCostToConnectRopes(int[] ropes) {
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        for (int rope : ropes) {
            pq.offer(rope);
        }
        
        int totalCost = 0;
        while (pq.size() > 1) {
            int first = pq.poll();
            int second = pq.poll();
            
            int currentCost = first + second;
            totalCost += currentCost;
            
            pq.offer(currentCost);
        }
        
        return totalCost;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log N)$.
- **Không gian (Space):** $O(N)$.

---

## 29. Skyline Problem
**Đề bài chi tiết:**
Cho một danh sách các toà nhà biểu diễn bằng `[left, right, height]`. Vẽ đường viền trên không (Skyline) hình thành bởi tất cả các toà nhà. Kết quả là danh sách các toạ độ `[x, y]` đánh dấu sự thay đổi chiều cao.

**Phân tích thuật toán:**
Dùng kỹ thuật Sweep Line kết hợp Max-Heap. Tách mỗi toà nhà thành 2 sự kiện: Bắt đầu `(left, height)` và Kết thúc `(right, -height)`. Sắp xếp các sự kiện theo toạ độ `x`. Dùng Max-Heap để lưu trữ các chiều cao toà nhà đang hoạt động (quét qua `left` nhưng chưa đến `right`). Tại mỗi sự kiện, cập nhật Heap và nếu chiều cao lớn nhất trong Heap bị thay đổi, ghi lại điểm Skyline.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> getSkyline(int[][] buildings) {
        List<List<Integer>> result = new ArrayList<>();
        List<int[]> events = new ArrayList<>();
        
        for (int[] b : buildings) {
            events.add(new int[]{b[0], b[2]});   // Sự kiện bắt đầu
            events.add(new int[]{b[1], -b[2]});  // Sự kiện kết thúc
        }
        
        events.sort((a, b) -> {
            if (a[0] != b[0]) return Integer.compare(a[0], b[0]);
            return Integer.compare(b[1], a[1]); 
            // Nếu cùng x, toà cao hơn xử lý trước (bắt đầu), thấp hơn (kết thúc)
        });
        
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        maxHeap.offer(0); // Mặt đất
        int prevMaxHeight = 0;
        
        for (int[] event : events) {
            if (event[1] > 0) {
                maxHeap.offer(event[1]);
            } else {
                maxHeap.remove(-event[1]); // Cảnh báo: remove trong PQ là O(N)
            }
            
            int currentMaxHeight = maxHeap.peek();
            if (prevMaxHeight != currentMaxHeight) {
                result.add(Arrays.asList(event[0], currentMaxHeight));
                prevMaxHeight = currentMaxHeight;
            }
        }
        return result;
    }
}
```
*(Ghi chú: Để tối ưu thao tác `remove` $O(N)$ trong Heap thành $O(\log N)$, thực tế người ta thường dùng `TreeMap` thay cho `PriorityQueue`).*

**Độ phức tạp:**
- **Thời gian (Time):** $O(N^2)$ với PriorityQueue do thao tác remove (hoặc $O(N \log N)$ nếu dùng TreeMap).
- **Không gian (Space):** $O(N)$.

---

## 30. Sliding Window Maximum
**Đề bài chi tiết:**
Cho mảng `nums` và một cửa sổ trượt kích thước `k`. Cửa sổ di chuyển từ trái sang phải mỗi bước 1 vị trí. Trả về mảng chứa giá trị lớn nhất trong cửa sổ tại mỗi vị trí.

**Phân tích thuật toán:**
Mặc dù cách tối ưu nhất dùng cấu trúc Deque với $O(N)$, bài này hoàn toàn có thể dùng Max-Heap (ưu tiên giá trị lớn nhất). Max-Heap sẽ lưu trữ các cặp `[giá_trị, chỉ_số]`. Khi trượt cửa sổ, đẩy phần tử mới vào. Để loại bỏ các phần tử nằm ngoài cửa sổ (lazy deletion), chỉ cần kiểm tra xem đỉnh của Heap có chỉ số $< i - k + 1$ hay không, nếu có thì pop ra. Đỉnh Heap sau đó chính là giá trị lớn nhất.

**Mã nguồn Java:**
```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if (nums == null || nums.length == 0) return new int[0];
        
        int n = nums.length;
        int[] result = new int[n - k + 1];
        
        // Max-Heap chứa {giá trị, chỉ số}
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) -> Integer.compare(b[0], a[0]));
        
        for (int i = 0; i < n; i++) {
            maxHeap.offer(new int[]{nums[i], i});
            
            // Xoá lười (Lazy deletion): Xoá đỉnh nếu nó nằm ngoài window hiện tại
            while (maxHeap.peek()[1] <= i - k) {
                maxHeap.poll();
            }
            
            if (i >= k - 1) {
                result[i - k + 1] = maxHeap.peek()[0];
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log N)$ trong trường hợp xấu nhất (mảng tăng dần khiến Heap chứa tất cả phần tử).
- **Không gian (Space):** $O(N)$.
