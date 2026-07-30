# Các bài tập áp dụng Merge Sort

Dưới đây là danh sách 30 bài tập áp dụng từ cơ bản đến nâng cao của Merge Sort và các biến thể Divide and Conquer liên quan. 10 bài đầu tiên sẽ có phân tích và mã nguồn chi tiết.

## 1. Sort an Array
**Đề bài chi tiết:** Cho một mảng các số nguyên `nums`, hãy sắp xếp mảng theo thứ tự tăng dần và trả về. Bạn cần giải quyết bài toán với độ phức tạp `O(N log N)` và hạn chế tối đa sử dụng các hàm có sẵn.
**Phân tích thuật toán:** Sử dụng trực tiếp thuật toán Merge Sort cơ bản. Chia mảng thành hai nửa, đệ quy sắp xếp và sau đó hợp nhất lại.
**Độ phức tạp:** Thời gian: O(N log N) | Không gian: O(N) do mảng phụ `temp`.

**Mã nguồn Java:**
```java
class Solution {
    public int[] sortArray(int[] nums) {
        if (nums == null || nums.length <= 1) return nums;
        int[] temp = new int[nums.length];
        mergeSort(nums, temp, 0, nums.length - 1);
        return nums;
    }
    
    private void mergeSort(int[] nums, int[] temp, int left, int right) {
        if (left >= right) return;
        int mid = left + (right - left) / 2;
        mergeSort(nums, temp, left, mid);
        mergeSort(nums, temp, mid + 1, right);
        merge(nums, temp, left, mid, right);
    }
    
    private void merge(int[] nums, int[] temp, int left, int mid, int right) {
        for (int i = left; i <= right; i++) {
            temp[i] = nums[i];
        }
        int i = left, j = mid + 1, k = left;
        while (i <= mid && j <= right) {
            if (temp[i] <= temp[j]) nums[k++] = temp[i++];
            else nums[k++] = temp[j++];
        }
        while (i <= mid) nums[k++] = temp[i++];
    }
}
```

---

## 2. Sort List
**Đề bài chi tiết:** Sắp xếp một Linked List (Danh sách liên kết) theo thứ tự tăng dần với độ phức tạp `O(N log N)` và bộ nhớ bổ sung O(1).
**Phân tích thuật toán:** Do cần O(1) space, Merge Sort kết hợp quy trình Bottom-Up được khuyến khích. Ở đây để dễ hiểu ta dùng phương pháp chia đôi danh sách bằng Slow và Fast pointer (sẽ tốn O(log N) cho call stack, chấp nhận được trong interview).
**Độ phức tạp:** Thời gian: O(N log N) | Không gian: O(log N) (Đệ quy stack).

**Mã nguồn Java:**
```java
class Solution {
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode mid = getMid(head);
        ListNode rightHead = mid.next;
        mid.next = null;
        
        ListNode left = sortList(head);
        ListNode right = sortList(rightHead);
        
        return merge(left, right);
    }
    
    private ListNode getMid(ListNode head) {
        ListNode slow = head, fast = head.next;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
    
    private ListNode merge(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(0);
        ListNode curr = dummy;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                curr.next = l1;
                l1 = l1.next;
            } else {
                curr.next = l2;
                l2 = l2.next;
            }
            curr = curr.next;
        }
        curr.next = (l1 != null) ? l1 : l2;
        return dummy.next;
    }
}
```

---

## 3. Count of Smaller Numbers After Self
**Đề bài chi tiết:** Cho một mảng nguyên `nums`. Bạn phải trả về một mảng mới `counts` trong đó `counts[i]` là số lượng các phần tử có giá trị bé hơn `nums[i]` nằm ở bên phải của `nums[i]`.
**Phân tích thuật toán:** Dùng Merge Sort để đếm số. Trong quá trình trộn nửa trái và nửa phải, nếu phần tử ở nửa phải nhỏ hơn, mọi phần tử còn lại ở nửa trái cũng sẽ có phần tử bên phải nhỏ hơn nó. Lưu ý, ta cần lưu giữ original index của từng phần tử trong quá trình sort.
**Độ phức tạp:** Thời gian: O(N log N) | Không gian: O(N).

**Mã nguồn Java:**
```java
class Solution {
    class Item {
        int val, index;
        public Item(int val, int index) { this.val = val; this.index = index; }
    }
    
    public List<Integer> countSmaller(int[] nums) {
        int n = nums.length;
        int[] result = new int[n];
        Item[] items = new Item[n];
        for (int i = 0; i < n; i++) items[i] = new Item(nums[i], i);
        
        mergeSort(items, new Item[n], 0, n - 1, result);
        
        List<Integer> resList = new ArrayList<>();
        for (int x : result) resList.add(x);
        return resList;
    }
    
    private void mergeSort(Item[] items, Item[] temp, int left, int right, int[] result) {
        if (left >= right) return;
        int mid = left + (right - left) / 2;
        mergeSort(items, temp, left, mid, result);
        mergeSort(items, temp, mid + 1, right, result);
        
        for (int i = left; i <= right; i++) temp[i] = items[i];
        
        int i = left, j = mid + 1, k = left, rightCounter = 0;
        while (i <= mid && j <= right) {
            if (temp[i].val <= temp[j].val) {
                result[temp[i].index] += rightCounter;
                items[k++] = temp[i++];
            } else {
                rightCounter++;
                items[k++] = temp[j++];
            }
        }
        while (i <= mid) {
            result[temp[i].index] += rightCounter;
            items[k++] = temp[i++];
        }
    }
}
```

---

## 4. Reverse Pairs
**Đề bài chi tiết:** Cho một mảng `nums`. Một Reverse Pair là cặp `(i, j)` mà `i < j` và `nums[i] > 2 * nums[j]`. Trả về số lượng các Reverse Pairs trong mảng.
**Phân tích thuật toán:** Một dạng của đếm Inversions. Tuy nhiên ta không đếm khi đang gộp mà thực hiện đếm riêng trước khi gộp 2 nửa, bằng hai con trỏ trên mảng `left` và mảng `right` đã được sắp xếp, vì điều kiện là `> 2 * nums[j]`.
**Độ phức tạp:** Thời gian: O(N log N) | Không gian: O(N).

**Mã nguồn Java:**
```java
class Solution {
    public int reversePairs(int[] nums) {
        return mergeSortAndCount(nums, 0, nums.length - 1);
    }
    
    private int mergeSortAndCount(int[] nums, int left, int right) {
        if (left >= right) return 0;
        int mid = left + (right - left) / 2;
        int count = mergeSortAndCount(nums, left, mid) + mergeSortAndCount(nums, mid + 1, right);
        
        int j = mid + 1;
        for (int i = left; i <= mid; i++) {
            while (j <= right && nums[i] / 2.0 > nums[j]) j++;
            count += j - (mid + 1);
        }
        
        merge(nums, left, mid, right);
        return count;
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
        for (int p = 0; p < temp.length; p++) nums[left + p] = temp[p];
    }
}
```

---

## 5. Global and Local Inversions
**Đề bài chi tiết:** Một mảng độ dài N có chứa các giá trị từ `0` đến `N-1`. Global Inversion là số lượng `i < j` với `A[i] > A[j]`. Local Inversion là số lượng `i` mà `A[i] > A[i+1]`. Hãy kiểm tra xem mảng có số lượng Global Inversion bằng số Local Inversion hay không.
**Phân tích thuật toán:** Mọi Local Inversion đều là Global. Để hai số lượng bằng nhau thì mọi Global Inversion không thể nhảy cách nhau từ 2 khoảng cách trở lên. Có thể giải bằng Math O(N). Dưới góc độ chia để trị/Merge, ta cũng có thể đếm Inversions bình thường bằng Merge Sort và so sánh.
**Độ phức tạp:** Thời gian: O(N) theo mẹo hoặc O(N log N) đếm Merge Sort | Không gian: O(1).

**Mã nguồn Java (Tối ưu Toán O(N)):**
```java
class Solution {
    public boolean isIdealPermutation(int[] A) {
        int max = -1;
        // Kiểm tra xem có phần tử nào lớn hơn A[i+2] không.
        for (int i = 0; i < A.length - 2; i++) {
            max = Math.max(max, A[i]);
            if (max > A[i + 2]) {
                return false;
            }
        }
        return true;
    }
}
```

---

## 6. Create Sorted Array through Instructions
**Đề bài chi tiết:** Cho danh sách các chỉ dẫn chèn phần tử `instructions`. Mỗi phần tử chèn vào chi phí tốn `min(số phần tử nhỏ hơn nghiêm ngặt, số phần tử lớn hơn nghiêm ngặt)`. Tính tổng chi phí sau khi chèn.
**Phân tích thuật toán:** Bài này tương tự bài đếm số lượng nhỏ hơn nhưng thêm cả đếm số lượng lớn hơn. Có thể giải bằng Binary Indexed Tree hoặc Merge Sort. Cần theo dõi đếm các vị trí.
**Độ phức tạp:** Thời gian: O(N log N) | Không gian: O(N).

**Mã nguồn Java (Sử dụng Binary Indexed Tree):**
```java
class Solution {
    int[] tree;
    public int createSortedArray(int[] instructions) {
        int max = 0;
        for (int x : instructions) max = Math.max(max, x);
        tree = new int[max + 1];
        
        long cost = 0;
        long MOD = 1_000_000_007;
        
        for (int i = 0; i < instructions.length; i++) {
            int x = instructions[i];
            int less = query(x - 1);
            int greater = i - query(x);
            cost = (cost + Math.min(less, greater)) % MOD;
            update(x, 1, max);
        }
        return (int)cost;
    }
    
    private void update(int index, int val, int max) {
        for (; index <= max; index += index & -index) tree[index] += val;
    }
    
    private int query(int index) {
        int sum = 0;
        for (; index > 0; index -= index & -index) sum += tree[index];
        return sum;
    }
}
```

---

## 7. Count of Range Sum
**Đề bài chi tiết:** Cho mảng `nums`, tìm số lượng mảng con sao cho tổng của mảng con nằm trong khoảng `[lower, upper]`.
**Phân tích thuật toán:** Sử dụng Mảng Tiền Tố (Prefix Sum). Dùng Merge Sort để đếm: khi kết hợp 2 nửa Prefix Sum `left` và `right`, với mỗi `left[i]`, ta dùng 2 con trỏ bên mảng `right` để tìm ra đoạn `[j, k)` thỏa mãn `lower <= right[index] - left[i] <= upper`.
**Độ phức tạp:** Thời gian: O(N log N) | Không gian: O(N).

**Mã nguồn Java:**
```java
class Solution {
    public int countRangeSum(int[] nums, int lower, int upper) {
        int n = nums.length;
        long[] prefixSum = new long[n + 1];
        for (int i = 0; i < n; i++) prefixSum[i + 1] = prefixSum[i] + nums[i];
        return mergeSort(prefixSum, 0, n + 1, lower, upper);
    }
    
    private int mergeSort(long[] sums, int start, int end, int lower, int upper) {
        if (end - start <= 1) return 0;
        int mid = start + (end - start) / 2;
        int count = mergeSort(sums, start, mid, lower, upper) + mergeSort(sums, mid, end, lower, upper);
        
        int j = mid, k = mid, t = mid;
        long[] temp = new long[end - start];
        int r = 0;
        
        for (int i = start; i < mid; i++) {
            while (k < end && sums[k] - sums[i] < lower) k++;
            while (j < end && sums[j] - sums[i] <= upper) j++;
            count += j - k;
            
            while (t < end && sums[t] < sums[i]) temp[r++] = sums[t++];
            temp[r++] = sums[i];
        }
        
        System.arraycopy(temp, 0, sums, start, r);
        return count;
    }
}
```

---

## 8. Merge Two Sorted Lists
**Đề bài chi tiết:** Hợp nhất hai danh sách liên kết đã sắp xếp thành một danh sách liên kết mới đã được sắp xếp.
**Phân tích thuật toán:** Đây chính là quy trình Merge lõi của Merge Sort. Duyệt bằng hai con trỏ trên 2 List và tạo node liên kết sang list nhỏ hơn.
**Độ phức tạp:** Thời gian: O(N + M) | Không gian: O(1).

**Mã nguồn Java:**
```java
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        ListNode dummy = new ListNode(-1);
        ListNode current = dummy;
        
        while (list1 != null && list2 != null) {
            if (list1.val <= list2.val) {
                current.next = list1;
                list1 = list1.next;
            } else {
                current.next = list2;
                list2 = list2.next;
            }
            current = current.next;
        }
        current.next = (list1 != null) ? list1 : list2;
        return dummy.next;
    }
}
```

---

## 9. Number of Ships in a Rectangle
**Đề bài chi tiết:** Có một số lượng tàu nằm trong một khung hình chữ nhật lớn. Bạn có API `hasShips(topRight, bottomLeft)`. Hãy đếm số lượng tàu có trong khung bằng tối đa 400 lượt gọi API. Mỗi điểm chỉ có 1 tàu.
**Phân tích thuật toán:** Chia để trị trên không gian 2D (Quad-tree/Divide and Conquer). Chia khung chữ nhật thành 4 phần bằng nhau và gọi đệ quy. Nếu vùng không có tàu, bỏ qua. Khi chia đến cấp một tọa độ 1x1 mà có tàu thì đếm là 1.
**Độ phức tạp:** Thời gian: Tùy thuộc cấu trúc (Max 400 calls) | Không gian: O(log R).

**Mã nguồn Java:**
```java
class Solution {
    public int countShips(Sea sea, int[] topRight, int[] bottomLeft) {
        if (!sea.hasShips(topRight, bottomLeft)) return 0;
        
        if (topRight[0] == bottomLeft[0] && topRight[1] == bottomLeft[1]) return 1;
        
        int midX = (topRight[0] + bottomLeft[0]) / 2;
        int midY = (topRight[1] + bottomLeft[1]) / 2;
        
        int ships = 0;
        // Góc trên phải
        ships += countShips(sea, topRight, new int[]{midX + 1, midY + 1});
        // Góc trên trái
        ships += countShips(sea, new int[]{midX, topRight[1]}, new int[]{bottomLeft[0], midY + 1});
        // Góc dưới phải
        ships += countShips(sea, new int[]{topRight[0], midY}, new int[]{midX + 1, bottomLeft[1]});
        // Góc dưới trái
        ships += countShips(sea, new int[]{midX, midY}, bottomLeft);
        
        return ships;
    }
}
```

---

## 10. Beautiful Array
**Đề bài chi tiết:** Cho một số N. Trả về mảng "Beautiful Array". Một mảng Beautiful nếu không có `i < k < j` nào thỏa mãn `nums[i] + nums[j] == 2 * nums[k]`.
**Phân tích thuật toán:** Sử dụng tính chất toán học của phân tách và chia để trị. Phân tách mảng theo số chẵn và số lẻ. Số lẻ * 2 + 1, và Số chẵn * 2. Một nửa toàn số chẵn và một nửa toàn số lẻ thì trung bình cộng của số chẵn và lẻ luôn không phải là số nguyên => loại bỏ hoàn toàn khả năng vi phạm ở giữa nửa này và nửa kia.
**Độ phức tạp:** Thời gian: O(N log N) | Không gian: O(N log N).

**Mã nguồn Java:**
```java
class Solution {
    public int[] beautifulArray(int n) {
        ArrayList<Integer> res = new ArrayList<>();
        res.add(1);
        while (res.size() < n) {
            ArrayList<Integer> temp = new ArrayList<>();
            // Phần lẻ
            for (int i : res) if (i * 2 - 1 <= n) temp.add(i * 2 - 1);
            // Phần chẵn
            for (int i : res) if (i * 2 <= n) temp.add(i * 2);
            res = temp;
        }
        return res.stream().mapToInt(i -> i).toArray();
    }
}
```

---

## 11. Merge k Sorted Lists
**Đề bài chi tiết:** Cho một mảng `lists` gồm `k` danh sách liên kết (Linked List), mỗi danh sách đã được sắp xếp theo thứ tự tăng dần. Hãy hợp nhất tất cả các danh sách liên kết này thành một danh sách liên kết duy nhất được sắp xếp và trả về nó.
**Phân tích thuật toán:** Sử dụng phương pháp Divide and Conquer tương tự như Merge Sort. Chúng ta sẽ ghép từng cặp danh sách liên kết lại với nhau bằng hàm `mergeTwoLists`, lặp đi lặp lại quá trình này cho đến khi chỉ còn lại một danh sách duy nhất. Quá trình chia đôi và gộp này giúp giảm đáng kể số lần duyệt qua các node so với việc gộp tuần tự.
**Độ phức tạp:** Thời gian: O(N log k) với N là tổng số node của tất cả k lists | Không gian: O(log k) do call stack của đệ quy, hoặc O(1) nếu duyệt bằng vòng lặp.

**Mã nguồn Java:**
```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) return null;
        return merge(lists, 0, lists.length - 1);
    }
    
    private ListNode merge(ListNode[] lists, int left, int right) {
        if (left == right) return lists[left];
        int mid = left + (right - left) / 2;
        ListNode l1 = merge(lists, left, mid);
        ListNode l2 = merge(lists, mid + 1, right);
        return mergeTwoLists(l1, l2);
    }
    
    private ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(0);
        ListNode curr = dummy;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                curr.next = l1;
                l1 = l1.next;
            } else {
                curr.next = l2;
                l2 = l2.next;
            }
            curr = curr.next;
        }
        curr.next = (l1 != null) ? l1 : l2;
        return dummy.next;
    }
}
```

---

## 12. Wiggle Sort II
**Đề bài chi tiết:** Cho một mảng số nguyên `nums`, hãy sắp xếp lại nó sao cho `nums[0] < nums[1] > nums[2] < nums[3]...`. Có thể giả định rằng đầu vào luôn có giải pháp hợp lệ.
**Phân tích thuật toán:** Một cách giải đơn giản dựa vào Merge/Sort là sắp xếp mảng trước, sau đó chia mảng thành hai nửa: nửa nhỏ và nửa lớn. Ta sẽ chèn xen kẽ các phần tử từ nửa nhỏ và nửa lớn vào mảng kết quả. Để đảm bảo các phần tử bằng nhau ở giữa không bị xếp cạnh nhau, ta cần lấy các phần tử từ cuối của mỗi nửa (tức là đi lùi) để chèn xen kẽ.
**Độ phức tạp:** Thời gian: O(N log N) | Không gian: O(N) do dùng mảng phụ `temp`.

**Mã nguồn Java:**
```java
class Solution {
    public void wiggleSort(int[] nums) {
        int[] temp = nums.clone();
        Arrays.sort(temp);
        int n = nums.length;
        
        int left = (n - 1) / 2;
        int right = n - 1;
        
        for (int i = 0; i < n; i++) {
            if (i % 2 == 0) {
                nums[i] = temp[left--];
            } else {
                nums[i] = temp[right--];
            }
        }
    }
}
```

---

## 13. The Skyline Problem
**Đề bài chi tiết:** Cho mảng `buildings` biểu diễn các tòa nhà, mỗi tòa nhà được xác định bởi `[left, right, height]`. Trả về hình bóng (skyline) của các tòa nhà này được tạo thành khi nhìn từ xa, biểu diễn dưới dạng danh sách các điểm ngoặt (tọa độ x và độ cao y mới).
**Phân tích thuật toán:** Sử dụng phương pháp Divide and Conquer (tương tự Merge Sort). Chia danh sách các tòa nhà thành hai nửa, đệ quy tìm skyline của mỗi nửa, sau đó hợp nhất hai skyline lại với nhau. Khi hợp nhất, dùng hai con trỏ duyệt qua hai tập skyline, cập nhật độ cao hiện tại của hai tập và thêm điểm có độ cao lớn hơn vào kết quả.
**Độ phức tạp:** Thời gian: O(N log N) | Không gian: O(N) đệ quy stack và lưu trữ.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> getSkyline(int[][] buildings) {
        if (buildings.length == 0) return new ArrayList<>();
        return divideAndConquer(buildings, 0, buildings.length - 1);
    }
    
    private List<List<Integer>> divideAndConquer(int[][] buildings, int left, int right) {
        List<List<Integer>> res = new ArrayList<>();
        if (left == right) {
            res.add(Arrays.asList(buildings[left][0], buildings[left][2]));
            res.add(Arrays.asList(buildings[left][1], 0));
            return res;
        }
        
        int mid = left + (right - left) / 2;
        List<List<Integer>> leftSkyline = divideAndConquer(buildings, left, mid);
        List<List<Integer>> rightSkyline = divideAndConquer(buildings, mid + 1, right);
        
        return mergeSkylines(leftSkyline, rightSkyline);
    }
    
    private List<List<Integer>> mergeSkylines(List<List<Integer>> left, List<List<Integer>> right) {
        int h1 = 0, h2 = 0;
        int i = 0, j = 0;
        List<List<Integer>> res = new ArrayList<>();
        
        while (i < left.size() && j < right.size()) {
            int x, h;
            if (left.get(i).get(0) < right.get(j).get(0)) {
                x = left.get(i).get(0);
                h1 = left.get(i).get(1);
                i++;
            } else if (left.get(i).get(0) > right.get(j).get(0)) {
                x = right.get(j).get(0);
                h2 = right.get(j).get(1);
                j++;
            } else {
                x = left.get(i).get(0);
                h1 = left.get(i).get(1);
                h2 = right.get(j).get(1);
                i++;
                j++;
            }
            
            h = Math.max(h1, h2);
            if (res.isEmpty() || res.get(res.size() - 1).get(1) != h) {
                res.add(Arrays.asList(x, h));
            }
        }
        
        while (i < left.size()) res.add(left.get(i++));
        while (j < right.size()) res.add(right.get(j++));
        
        return res;
    }
}
```

---

## 14. Median of Two Sorted Arrays
**Đề bài chi tiết:** Cho hai mảng đã được sắp xếp `nums1` và `nums2` có kích thước lần lượt là `m` và `n`. Hãy trả về giá trị trung vị (median) của hai mảng này với độ phức tạp thời gian mong muốn là `O(log(m+n))`.
**Phân tích thuật toán:** Bài toán này có thể hiểu là tìm phần tử lớn thứ k trong hai mảng đã sắp xếp. Sử dụng phương pháp Divide and Conquer kết hợp Binary Search để loại bỏ k/2 phần tử mỗi bước. Phương pháp này thao tác như một thao tác "Virtual Merge" giúp thu hẹp phạm vi tìm kiếm nhanh chóng.
**Độ phức tạp:** Thời gian: O(log(m + n)) | Không gian: O(log(m + n)) do stack đệ quy.

**Mã nguồn Java:**
```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int totalLength = nums1.length + nums2.length;
        if (totalLength % 2 == 1) {
            return findKth(nums1, 0, nums2, 0, totalLength / 2 + 1);
        } else {
            return (findKth(nums1, 0, nums2, 0, totalLength / 2) + 
                    findKth(nums1, 0, nums2, 0, totalLength / 2 + 1)) / 2.0;
        }
    }
    
    private int findKth(int[] nums1, int i, int[] nums2, int j, int k) {
        if (i >= nums1.length) return nums2[j + k - 1];
        if (j >= nums2.length) return nums1[i + k - 1];
        if (k == 1) return Math.min(nums1[i], nums2[j]);
        
        int midVal1 = (i + k / 2 - 1 < nums1.length) ? nums1[i + k / 2 - 1] : Integer.MAX_VALUE;
        int midVal2 = (j + k / 2 - 1 < nums2.length) ? nums2[j + k / 2 - 1] : Integer.MAX_VALUE;
        
        if (midVal1 < midVal2) {
            return findKth(nums1, i + k / 2, nums2, j, k - k / 2);
        } else {
            return findKth(nums1, i, nums2, j + k / 2, k - k / 2);
        }
    }
}
```

---

## 15. Burst Balloons
**Đề bài chi tiết:** Có `n` quả bóng bay được đánh số từ `0` đến `n-1`, mỗi quả bóng chứa một số điểm tương ứng `nums[i]`. Khi làm nổ quả bóng `i`, bạn sẽ nhận được `nums[i-1] * nums[i] * nums[i+1]` điểm. Tính số điểm tối đa bạn có thể thu được bằng cách làm nổ các quả bóng. Hai bên biên có thể xem như bóng có giá trị 1.
**Phân tích thuật toán:** Dùng Quy hoạch động (Dynamic Programming) kết hợp với tư duy Divide and Conquer (Chia để trị có nhớ). Thay vì nghĩ xem nên nổ quả bóng nào đầu tiên, ta xem quả bóng `k` nào sẽ nổ **cuối cùng** trong khoảng `[left, right]`. Việc này tách biệt hoàn toàn hai nửa trái và phải của `k`.
**Độ phức tạp:** Thời gian: O(N^3) | Không gian: O(N^2).

**Mã nguồn Java:**
```java
class Solution {
    public int maxCoins(int[] nums) {
        int n = nums.length;
        int[] arr = new int[n + 2];
        arr[0] = 1; arr[n + 1] = 1;
        for (int i = 0; i < n; i++) arr[i + 1] = nums[i];
        
        int[][] memo = new int[n + 2][n + 2];
        return divideAndConquer(arr, 0, n + 1, memo);
    }
    
    private int divideAndConquer(int[] arr, int left, int right, int[][] memo) {
        if (left + 1 == right) return 0;
        if (memo[left][right] > 0) return memo[left][right];
        
        int ans = 0;
        for (int i = left + 1; i < right; i++) {
            ans = Math.max(ans, 
                arr[left] * arr[i] * arr[right] 
                + divideAndConquer(arr, left, i, memo) 
                + divideAndConquer(arr, i, right, memo)
            );
        }
        memo[left][right] = ans;
        return ans;
    }
}
```

---

## 16. Different Ways to Add Parentheses
**Đề bài chi tiết:** Cho một biểu thức dạng chuỗi gồm số và toán tử (`+`, `-`, `*`). Trả về tất cả các kết quả khả thi có thể đạt được từ các cách nhóm biểu thức bằng dấu ngoặc khác nhau.
**Phân tích thuật toán:** Bài toán áp dụng điển hình mô hình Divide and Conquer. Khi duyệt qua chuỗi và gặp một toán tử, ta chia chuỗi biểu thức thành hai nửa trái và phải. Đệ quy giải để tìm tất cả kết quả của phần trái, phần phải, sau đó hợp nhất kết quả từ hai phía lại bằng toán tử hiện tại.
**Độ phức tạp:** Thời gian: O(C_n) với C_n là số Catalan thứ n | Không gian: Tương đương kết quả lưu trữ.

**Mã nguồn Java:**
```java
class Solution {
    Map<String, List<Integer>> memo = new HashMap<>();
    
    public List<Integer> diffWaysToCompute(String expression) {
        if (memo.containsKey(expression)) return memo.get(expression);
        
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < expression.length(); i++) {
            char c = expression.charAt(i);
            if (c == '+' || c == '-' || c == '*') {
                String leftPart = expression.substring(0, i);
                String rightPart = expression.substring(i + 1);
                
                List<Integer> leftRes = diffWaysToCompute(leftPart);
                List<Integer> rightRes = diffWaysToCompute(rightPart);
                
                for (int l : leftRes) {
                    for (int r : rightRes) {
                        if (c == '+') res.add(l + r);
                        else if (c == '-') res.add(l - r);
                        else res.add(l * r);
                    }
                }
            }
        }
        
        // Nếu chuỗi chỉ chứa số
        if (res.isEmpty()) {
            res.add(Integer.parseInt(expression));
        }
        
        memo.put(expression, res);
        return res;
    }
}
```

---

## 17. Maximum Subarray
**Đề bài chi tiết:** Cho một mảng số nguyên `nums`, hãy tìm mảng con liên tiếp (chứa ít nhất một số) có tổng lớn nhất và trả về tổng đó. (Yêu cầu tiếp cận bằng Chia để trị - O(N log N)).
**Phân tích thuật toán:** Mặc dù thuật toán Kadane tối ưu (O(N)), cách chia để trị O(N log N) lại giúp củng cố tư duy gộp mảng (merge). Chia mảng thành nửa trái và nửa phải. Mảng con lớn nhất có thể nằm trọn trong nửa trái, nửa phải, hoặc băng qua điểm giữa (crossing mid). Cần tính giá trị lớn nhất băng qua điểm giữa và so sánh.
**Độ phức tạp:** Thời gian: O(N log N) | Không gian: O(log N) do đệ quy.

**Mã nguồn Java:**
```java
class Solution {
    public int maxSubArray(int[] nums) {
        return maxSubArrayDivideConquer(nums, 0, nums.length - 1);
    }
    
    private int maxSubArrayDivideConquer(int[] nums, int left, int right) {
        if (left == right) return nums[left];
        
        int mid = left + (right - left) / 2;
        int leftMax = maxSubArrayDivideConquer(nums, left, mid);
        int rightMax = maxSubArrayDivideConquer(nums, mid + 1, right);
        int crossMax = maxCrossingSum(nums, left, mid, right);
        
        return Math.max(Math.max(leftMax, rightMax), crossMax);
    }
    
    private int maxCrossingSum(int[] nums, int left, int mid, int right) {
        int sum = 0;
        int leftPartSum = Integer.MIN_VALUE;
        for (int i = mid; i >= left; i--) {
            sum += nums[i];
            leftPartSum = Math.max(leftPartSum, sum);
        }
        
        sum = 0;
        int rightPartSum = Integer.MIN_VALUE;
        for (int i = mid + 1; i <= right; i++) {
            sum += nums[i];
            rightPartSum = Math.max(rightPartSum, sum);
        }
        
        return leftPartSum + rightPartSum;
    }
}
```

---

## 18. Search a 2D Matrix II
**Đề bài chi tiết:** Viết thuật toán tìm kiếm hiệu quả giá trị `target` trong ma trận `m x n`. Mỗi hàng sắp xếp tăng dần từ trái sang phải, mỗi cột sắp xếp tăng dần từ trên xuống dưới.
**Phân tích thuật toán:** Một hướng tiếp cận mang tính loại trừ tương tự Divide and Conquer là duyệt từ góc trên bên phải hoặc dưới bên trái. Nếu duyệt từ trên cùng bên phải, nếu phần tử hiện tại lớn hơn `target`, ta loại bỏ toàn bộ cột; nếu nhỏ hơn, ta loại bỏ toàn bộ hàng.
**Độ phức tạp:** Thời gian: O(M + N) | Không gian: O(1).

**Mã nguồn Java:**
```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return false;
        }
        
        int row = 0;
        int col = matrix[0].length - 1;
        
        // Bắt đầu từ góc trên bên phải
        while (row < matrix.length && col >= 0) {
            if (matrix[row][col] == target) {
                return true;
            } else if (matrix[row][col] > target) {
                col--; // Loại bỏ cột hiện tại
            } else {
                row++; // Loại bỏ hàng hiện tại
            }
        }
        
        return false;
    }
}
```

---

## 19. Kth Largest Element in an Array
**Đề bài chi tiết:** Cho một mảng nguyên `nums` chưa được sắp xếp và một số nguyên `k`. Tìm phần tử lớn thứ `k` trong mảng đó. Yêu cầu giải quyết trong O(N) trung bình.
**Phân tích thuật toán:** Ứng dụng QuickSelect, một thuật toán dựa trên nền tảng Chia để trị (Partitioning) tương tự QuickSort. Ta chọn một pivot và phân tách mảng. Dựa trên vị trí của pivot, ta chỉ việc tìm đệ quy ở một nửa mảng chứa phần tử cần tìm, giúp giảm đáng kể thời gian xuống O(N).
**Độ phức tạp:** Thời gian: O(N) ở trường hợp trung bình, O(N^2) ở trường hợp xấu nhất | Không gian: O(1) in-place hoặc O(log N) cho stack.

**Mã nguồn Java:**
```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        // Tìm phần tử có index (n - k) trên mảng nếu được xếp tăng dần
        int targetIndex = nums.length - k;
        return quickSelect(nums, 0, nums.length - 1, targetIndex);
    }
    
    private int quickSelect(int[] nums, int left, int right, int k) {
        if (left == right) return nums[left];
        
        int pivotIndex = partition(nums, left, right);
        
        if (pivotIndex == k) {
            return nums[k];
        } else if (pivotIndex < k) {
            return quickSelect(nums, pivotIndex + 1, right, k);
        } else {
            return quickSelect(nums, left, pivotIndex - 1, k);
        }
    }
    
    private int partition(int[] nums, int left, int right) {
        int pivot = nums[right];
        int i = left;
        for (int j = left; j < right; j++) {
            if (nums[j] <= pivot) {
                swap(nums, i, j);
                i++;
            }
        }
        swap(nums, i, right);
        return i;
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

---

## 20. Merge Intervals
**Đề bài chi tiết:** Cho một mảng `intervals` chứa các khoảng `[start, end]`. Hãy hợp nhất tất cả các khoảng chồng lấn (overlap) và trả về mảng các khoảng kết quả không còn chồng lấn (non-overlapping).
**Phân tích thuật toán:** Bài toán mô phỏng bước gộp của Merge Sort trên trục tuyến tính. Trước tiên ta sắp xếp các khoảng theo điểm xuất phát (`start`). Sau đó, duyệt qua các khoảng và gộp khoảng hiện tại với khoảng tiếp theo nếu khoảng tiếp theo bắt đầu trước hoặc bằng thời điểm kết thúc của khoảng hiện tại.
**Độ phức tạp:** Thời gian: O(N log N) cho việc sắp xếp | Không gian: O(N) cho mảng kết quả.

**Mã nguồn Java:**
```java
class Solution {
    public int[][] merge(int[][] intervals) {
        if (intervals.length <= 1) return intervals;
        
        // Sắp xếp các khoảng theo start time
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
        
        List<int[]> merged = new ArrayList<>();
        int[] currentInterval = intervals[0];
        merged.add(currentInterval);
        
        for (int[] interval : intervals) {
            int currentEnd = currentInterval[1];
            int nextBegin = interval[0];
            int nextEnd = interval[1];
            
            // Nếu có chồng lấn, tiến hành gộp
            if (currentEnd >= nextBegin) {
                currentInterval[1] = Math.max(currentEnd, nextEnd);
            } else {
                currentInterval = interval;
                merged.add(currentInterval);
            }
        }
        
        return merged.toArray(new int[merged.size()][]);
    }
}
```

---

## 21. Insert Interval
**Đề bài chi tiết:** Cho một mảng các khoảng `intervals` không chồng chéo và đã được sắp xếp theo điểm xuất phát. Hãy chèn một `newInterval` vào mảng sao cho các khoảng vẫn không chồng chéo (có thể cần gộp các khoảng bị chồng lấn). Trả về mảng các khoảng sau khi chèn.
**Phân tích thuật toán:** Duyệt tuần tự mảng `intervals`. Những khoảng kết thúc trước khi `newInterval` bắt đầu thì thêm luôn vào kết quả. Khi có sự chồng lấn, ta gộp bằng cách lấy `min` điểm bắt đầu và `max` điểm kết thúc. Cuối cùng, thêm các khoảng còn lại vào kết quả. Mặc dù không dùng đệ quy chia để trị, bài toán này áp dụng bước gộp (merge) một cách tuyến tính tương tự như Merge Sort trên mảng đã sắp.
**Độ phức tạp:** Thời gian: O(N) | Không gian: O(N) cho mảng kết quả.

**Mã nguồn Java:**
```java
class Solution {
    public int[][] insert(int[][] intervals, int[] newInterval) {
        List<int[]> result = new ArrayList<>();
        int i = 0;
        int n = intervals.length;
        
        // Thêm các khoảng nằm hoàn toàn bên trái khoảng mới
        while (i < n && intervals[i][1] < newInterval[0]) {
            result.add(intervals[i]);
            i++;
        }
        
        // Gộp các khoảng bị chồng lấn với khoảng mới
        while (i < n && intervals[i][0] <= newInterval[1]) {
            newInterval[0] = Math.min(newInterval[0], intervals[i][0]);
            newInterval[1] = Math.max(newInterval[1], intervals[i][1]);
            i++;
        }
        result.add(newInterval);
        
        // Thêm các khoảng nằm hoàn toàn bên phải
        while (i < n) {
            result.add(intervals[i]);
            i++;
        }
        
        return result.toArray(new int[result.size()][]);
    }
}
```

---

## 22. K Closest Points to Origin
**Đề bài chi tiết:** Cho mảng `points` đại diện cho tọa độ các điểm trên mặt phẳng 2D và số nguyên `k`. Tìm `k` điểm gần gốc tọa độ `(0, 0)` nhất.
**Phân tích thuật toán:** Khoảng cách tới gốc tọa độ được tính bằng `x^2 + y^2`. Ta có thể dùng thuật toán QuickSelect (chia để trị dựa trên Partition) để gom `k` phần tử nhỏ nhất về bên trái mảng chỉ trong thời gian trung bình O(N).
**Độ phức tạp:** Thời gian: O(N) trung bình, O(N^2) tệ nhất | Không gian: O(1) in-place hoặc O(log N) cho stack đệ quy.

**Mã nguồn Java:**
```java
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
        int pivotDist = pivot[0] * pivot[0] + pivot[1] * pivot[1];
        int i = left;
        
        for (int j = left; j < right; j++) {
            int dist = points[j][0] * points[j][0] + points[j][1] * points[j][1];
            if (dist <= pivotDist) {
                swap(points, i, j);
                i++;
            }
        }
        swap(points, i, right);
        return i;
    }
    
    private void swap(int[][] points, int i, int j) {
        int[] temp = points[i];
        points[i] = points[j];
        points[j] = temp;
    }
}
```

---

## 23. Longest Substring with At Least K Repeating Characters
**Đề bài chi tiết:** Cho chuỗi `s` và số nguyên `k`, hãy tìm độ dài chuỗi con dài nhất mà trong đó mỗi ký tự xuất hiện ít nhất `k` lần.
**Phân tích thuật toán:** Dùng Divide and Conquer. Đếm tần suất các ký tự trong chuỗi. Nếu một ký tự xuất hiện ít hơn `k` lần, nó không thể nằm trong chuỗi con thỏa mãn. Ta dùng ký tự này làm điểm cắt (split point) để chia chuỗi thành các chuỗi con nhỏ hơn và gọi đệ quy trên từng phần để tìm độ dài lớn nhất.
**Độ phức tạp:** Thời gian: O(N * 26) trong trường hợp xấu nhất | Không gian: O(26) cho đệ quy.

**Mã nguồn Java:**
```java
class Solution {
    public int longestSubstring(String s, int k) {
        if (s == null || s.length() == 0 || k > s.length()) return 0;
        
        int[] count = new int[26];
        for (int i = 0; i < s.length(); i++) {
            count[s.charAt(i) - 'a']++;
        }
        
        boolean valid = true;
        for (int i = 0; i < 26; i++) {
            if (count[i] > 0 && count[i] < k) {
                valid = false;
                break;
            }
        }
        
        if (valid) return s.length();
        
        int result = 0;
        int start = 0;
        for (int i = 0; i < s.length(); i++) {
            if (count[s.charAt(i) - 'a'] < k) {
                result = Math.max(result, longestSubstring(s.substring(start, i), k));
                start = i + 1;
            }
        }
        result = Math.max(result, longestSubstring(s.substring(start), k));
        
        return result;
    }
}
```

---

## 24. Find K-th Smallest Pair Distance
**Đề bài chi tiết:** Cho mảng `nums` và số `k`, hãy trả về khoảng cách tuyệt đối lớn thứ `k` của các cặp điểm trong mảng.
**Phân tích thuật toán:** Bài này tối ưu bằng cách kết hợp Chặt nhị phân (Binary Search) trên khoảng cách và Two Pointers để đếm số cặp có khoảng cách nhỏ hơn hoặc bằng một khoảng cách `mid` mục tiêu. Phương pháp Two Pointers hoạt động cực kỳ hiệu quả sau khi mảng đã được sắp xếp, bản thân việc này liên quan đến mô hình kiểm tra giá trị tương đồng Merge Process (duyệt tuyến tính song song).
**Độ phức tạp:** Thời gian: O(N log N + N log W) với W là khoảng cách tối đa | Không gian: O(1).

**Mã nguồn Java:**
```java
class Solution {
    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums);
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
    
    private int countPairs(int[] nums, int mid) {
        int count = 0;
        int j = 0;
        for (int i = 0; i < nums.length; i++) {
            while (j < nums.length && nums[j] - nums[i] <= mid) {
                j++;
            }
            count += j - i - 1;
        }
        return count;
    }
}
```

---

## 25. Sort Colors
**Đề bài chi tiết:** Cho mảng `nums` chứa các số 0 (đỏ), 1 (trắng) và 2 (xanh). Sắp xếp mảng in-place theo thứ tự 0, 1, 2. (Không sử dụng hàm sắp xếp có sẵn).
**Phân tích thuật toán:** Sử dụng phương pháp chia 3 vùng (3-way partitioning - tương tự Dutch National Flag problem), là nền tảng của thuật toán QuickSort. Sử dụng ba con trỏ: `low` để gom các số 0 về bên trái, `high` để gom số 2 về bên phải, và `current` để duyệt từ trái qua.
**Độ phức tạp:** Thời gian: O(N) (duyệt một lần) | Không gian: O(1).

**Mã nguồn Java:**
```java
class Solution {
    public void sortColors(int[] nums) {
        int low = 0, current = 0, high = nums.length - 1;
        
        while (current <= high) {
            if (nums[current] == 0) {
                swap(nums, current, low);
                current++;
                low++;
            } else if (nums[current] == 2) {
                swap(nums, current, high);
                high--;
            } else {
                current++;
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

---

## 26. Kth Smallest Number in Multiplication Table
**Đề bài chi tiết:** Cho một bảng cửu chương kích thước `m x n`. Bảng có các giá trị là `i * j` với `1 <= i <= m` và `1 <= j <= n`. Trả về phần tử nhỏ thứ `k` trong bảng này.
**Phân tích thuật toán:** Sử dụng Binary Search trên tập giá trị từ `1` đến `m * n`. Giống như chia để trị/chia nhị phân, để kiểm tra xem một số `mid` có phải là số lớn thứ `k` hay không, ta duyệt đếm số lượng số nhỏ hơn hoặc bằng `mid` ở từng hàng của bảng. Mỗi hàng là cấp số cộng nên chỉ việc tính bằng phép chia.
**Độ phức tạp:** Thời gian: O(M * log(M * N)) | Không gian: O(1).

**Mã nguồn Java:**
```java
class Solution {
    public int findKthNumber(int m, int n, int k) {
        int left = 1, right = m * n;
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            
            if (enough(mid, m, n, k)) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        
        return left;
    }
    
    private boolean enough(int x, int m, int n, int k) {
        int count = 0;
        for (int i = 1; i <= m; i++) {
            count += Math.min(x / i, n);
        }
        return count >= k;
    }
}
```

---

## 27. Number of Inversions in an array
**Đề bài chi tiết:** Cho một mảng `arr` gồm các số nguyên phân biệt. Tìm số lượng nghịch thế (inversions) trong mảng. Nghịch thế là một cặp `(i, j)` mà `i < j` và `arr[i] > arr[j]`.
**Phân tích thuật toán:** Một bài toán kinh điển hoàn hảo của Merge Sort. Quá trình Merge Sort đếm số nghịch thế mỗi khi phần tử bên phải `arr[j]` nhỏ hơn phần tử bên trái `arr[i]`. Khi đó, toàn bộ phần tử từ `i` đến cuối mảng bên trái đều tạo thành cặp nghịch thế với `arr[j]`.
**Độ phức tạp:** Thời gian: O(N log N) | Không gian: O(N) cho mảng phụ.

**Mã nguồn Java:**
```java
public class Solution {
    public static long countInversions(int[] arr) {
        if (arr == null || arr.length < 2) return 0;
        int[] temp = new int[arr.length];
        return mergeSortAndCount(arr, temp, 0, arr.length - 1);
    }
    
    private static long mergeSortAndCount(int[] arr, int[] temp, int left, int right) {
        long count = 0;
        if (left < right) {
            int mid = left + (right - left) / 2;
            count += mergeSortAndCount(arr, temp, left, mid);
            count += mergeSortAndCount(arr, temp, mid + 1, right);
            count += merge(arr, temp, left, mid, right);
        }
        return count;
    }
    
    private static long merge(int[] arr, int[] temp, int left, int mid, int right) {
        int i = left, j = mid + 1, k = left;
        long invCount = 0;
        
        while (i <= mid && j <= right) {
            if (arr[i] <= arr[j]) {
                temp[k++] = arr[i++];
            } else {
                temp[k++] = arr[j++];
                invCount += (mid - i + 1); // Đếm số phần tử còn lại của nửa trái
            }
        }
        
        while (i <= mid) temp[k++] = arr[i++];
        while (j <= right) temp[k++] = arr[j++];
        
        for (i = left; i <= right; i++) {
            arr[i] = temp[i];
        }
        
        return invCount;
    }
}
```

---

## 28. Largest Number
**Đề bài chi tiết:** Cho mảng các số nguyên không âm `nums`, hãy sắp xếp và ghép chúng lại thành số lớn nhất (trả về dưới dạng chuỗi).
**Phân tích thuật toán:** Thuật toán ghi đè lại phương thức so sánh của hệ thống (bản thân hệ thống Java dùng biến thể của Merge/Timsort). Cách so sánh hai chuỗi số `a` và `b` là so sánh giá trị của chuỗi ghép `a+b` và `b+a`.
**Độ phức tạp:** Thời gian: O(N log N) | Không gian: O(N) lưu chuỗi kết quả.

**Mã nguồn Java:**
```java
class Solution {
    public String largestNumber(int[] nums) {
        String[] asStrs = new String[nums.length];
        for (int i = 0; i < nums.length; i++) {
            asStrs[i] = String.valueOf(nums[i]);
        }
        
        Arrays.sort(asStrs, new Comparator<String>() {
            @Override
            public int compare(String a, String b) {
                String order1 = a + b;
                String order2 = b + a;
                // Sắp xếp giảm dần
                return order2.compareTo(order1);
            }
        });
        
        if (asStrs[0].equals("0")) {
            return "0";
        }
        
        StringBuilder largestNumberStr = new StringBuilder();
        for (String numAsStr : asStrs) {
            largestNumberStr.append(numAsStr);
        }
        
        return largestNumberStr.toString();
    }
}
```

---

## 29. Minimum Number of Arrows to Burst Balloons
**Đề bài chi tiết:** Có một số lượng quả bóng bay 2D trên không trung, được đại diện bởi khoảng `[x_start, x_end]`. Một mũi tên có thể được bắn từ x và đi dọc lên vỡ mọi quả bóng trong khoảng `x_start <= x <= x_end`. Tìm số mũi tên tối thiểu.
**Phân tích thuật toán:** Sử dụng Sorting tương tự như thao tác chuẩn bị gộp (Merge Interval). Sắp xếp bóng theo điểm kết thúc `x_end`. Nếu quả bóng kế tiếp bắt đầu sau quả bóng hiện tại kết thúc, ta cần thêm 1 mũi tên và cập nhật điểm bắn mũi tên tiếp theo vào `x_end` mới.
**Độ phức tạp:** Thời gian: O(N log N) | Không gian: O(1).

**Mã nguồn Java:**
```java
class Solution {
    public int findMinArrowShots(int[][] points) {
        if (points.length == 0) return 0;
        
        // Sắp xếp theo x_end tăng dần
        Arrays.sort(points, (a, b) -> Integer.compare(a[1], b[1]));
        
        int arrows = 1;
        int currentEnd = points[0][1];
        
        for (int i = 1; i < points.length; i++) {
            // Nếu khoảng bắt đầu của quả tiếp theo lớn hơn khoảng kết thúc của mũi tên trước đó
            if (points[i][0] > currentEnd) {
                arrows++;
                currentEnd = points[i][1]; // Cập nhật lại mốc ngắm mũi tên
            }
        }
        
        return arrows;
    }
}
```

---

## 30. Divide Two Integers
**Đề bài chi tiết:** Cho hai số nguyên `dividend` và `divisor`, hãy chia `dividend` cho `divisor` mà không dùng phép nhân, phép chia, phép mod (%). Trả về phần nguyên giới hạn trong vùng số nguyên 32-bit (có dấu).
**Phân tích thuật toán:** Áp dụng nguyên lý Chia để Trị dịch bit. Chúng ta dùng phép trừ dịch bit toán học để thay thế phép chia. Tìm luỹ thừa lớn nhất của 2 sao cho `divisor * 2^n <= dividend`, rồi trừ đi giá trị đó khỏi `dividend`, và cộng `2^n` vào kết quả, lặp lại cho đến khi `dividend` nhỏ hơn `divisor`.
**Độ phức tạp:** Thời gian: O(log N) với N là tỉ số | Không gian: O(1).

**Mã nguồn Java:**
```java
class Solution {
    public int divide(int dividend, int divisor) {
        if (dividend == Integer.MIN_VALUE && divisor == -1) {
            return Integer.MAX_VALUE;
        }
        
        int sign = (dividend > 0) ^ (divisor > 0) ? -1 : 1;
        
        long dvd = Math.abs((long) dividend);
        long dvs = Math.abs((long) divisor);
        int result = 0;
        
        while (dvd >= dvs) {
            long temp = dvs, multiple = 1;
            while (dvd >= (temp << 1)) {
                temp <<= 1;
                multiple <<= 1;
            }
            dvd -= temp;
            result += multiple;
        }
        
        return sign == 1 ? result : -result;
    }
}
```
