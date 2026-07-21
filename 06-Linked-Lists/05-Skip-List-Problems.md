---
title: Skip List Problems
description: 30 bài tập thực hành cấu trúc dữ liệu Skip List từ cơ bản đến nâng cao.
author: DSA Curriculum Writer
date: 2026-07-21
tags: [LinkedList, SkipList, Java, DSA, Concurrent, Coding]
---

# 30 Bài tập Skip List (Java 21)

## 1. Design Skip List
**Đề bài chi tiết:**
Thiết kế cấu trúc dữ liệu Skip List. Hỗ trợ các phương thức: `search(int target)`, `add(int num)`, `erase(int num)`.

**Phân tích thuật toán:**
- Dùng một mảng `forward` trong mỗi Node để lưu các liên kết ở nhiều mức khác nhau.
- Tung đồng xu (`random.nextDouble() < 0.5`) để quyết định mức (level) của node mới khi thêm.
- Giữ một mảng `update` để lưu các node cần cập nhật con trỏ khi `add` hoặc `erase`.

**Mã nguồn Java:**
```java
import java.util.Random;

class Skiplist {
    private static final double P = 0.5;
    private static final int MAX_LEVEL = 16;
    
    private class Node {
        int val;
        Node[] forward;
        public Node(int val, int level) {
            this.val = val;
            this.forward = new Node[level + 1];
        }
    }
    
    private Node head;
    private int level;
    private Random random;

    public Skiplist() {
        head = new Node(-1, MAX_LEVEL);
        level = 0;
        random = new Random();
    }
    
    private int randomLevel() {
        int lvl = 0;
        while (lvl < MAX_LEVEL && random.nextDouble() < P) {
            lvl++;
        }
        return lvl;
    }
    
    public boolean search(int target) {
        Node curr = head;
        for (int i = level; i >= 0; i--) {
            while (curr.forward[i] != null && curr.forward[i].val < target) {
                curr = curr.forward[i];
            }
        }
        curr = curr.forward[0];
        return curr != null && curr.val == target;
    }
    
    public void add(int num) {
        Node[] update = new Node[MAX_LEVEL + 1];
        Node curr = head;
        
        for (int i = level; i >= 0; i--) {
            while (curr.forward[i] != null && curr.forward[i].val < num) {
                curr = curr.forward[i];
            }
            update[i] = curr;
        }
        
        int newLevel = randomLevel();
        if (newLevel > level) {
            for (int i = level + 1; i <= newLevel; i++) {
                update[i] = head;
            }
            level = newLevel;
        }
        
        Node newNode = new Node(num, newLevel);
        for (int i = 0; i <= newLevel; i++) {
            newNode.forward[i] = update[i].forward[i];
            update[i].forward[i] = newNode;
        }
    }
    
    public boolean erase(int num) {
        Node[] update = new Node[MAX_LEVEL + 1];
        Node curr = head;
        
        for (int i = level; i >= 0; i--) {
            while (curr.forward[i] != null && curr.forward[i].val < num) {
                curr = curr.forward[i];
            }
            update[i] = curr;
        }
        
        curr = curr.forward[0];
        if (curr != null && curr.val == num) {
            for (int i = 0; i <= level; i++) {
                if (update[i].forward[i] != curr) break;
                update[i].forward[i] = curr.forward[i];
            }
            while (level > 0 && head.forward[level] == null) {
                level--;
            }
            return true;
        }
        return false;
    }
}
```

**Độ phức tạp:**
- Thời gian: Search/Add/Erase Expected `O(log N)`.
- Không gian: Expected `O(N)`.

## 2. Implement Search in Skip List
**Đề bài chi tiết:**
Cho một Skip List đã được thiết lập sẵn. Trả về đường đi (path) bao gồm các giá trị của các node mà thuật toán Search đã duyệt qua để tìm kiếm một `target`.

**Phân tích thuật toán:**
Tương tự như thuật toán Search cơ bản, nhưng thêm một List để lưu các node đã đi qua (bao gồm cả việc đi ngang và rớt xuống tầng tiếp theo).

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

class SkipListSearchPath {
    static class Node {
        int val;
        Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level + 1]; }
    }
    
    public List<Integer> getSearchPath(Node head, int level, int target) {
        List<Integer> path = new ArrayList<>();
        Node curr = head;
        
        for (int i = level; i >= 0; i--) {
            while (curr.forward[i] != null && curr.forward[i].val < target) {
                path.add(curr.val);
                curr = curr.forward[i];
            }
        }
        path.add(curr.val); // last node before target or equals
        
        if (curr.forward[0] != null && curr.forward[0].val == target) {
            path.add(target);
        }
        
        return path;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(log N)` (Expected).
- Không gian: `O(log N)` cho mảng path.

## 3. Implement Insert in Skip List with Custom Probability
**Đề bài chi tiết:**
Triển khai phương thức `add` vào Skip List nhưng có khả năng thay đổi tỷ lệ xác suất `p`. In ra phân phối các cấp độ sau khi thêm `N` phần tử với `p = 0.25`.

**Phân tích thuật toán:**
Định nghĩa một hàm random với tham số `p` linh hoạt. Tính lại hàm `add` và sau khi chèn xong một vòng lặp, chạy thống kê số lượng level.

**Mã nguồn Java:**
```java
import java.util.Random;

class SkipListCustomP {
    private double p;
    private int maxLevel;
    private Random random;
    
    public SkipListCustomP(double p, int maxLevel) {
        this.p = p;
        this.maxLevel = maxLevel;
        this.random = new Random();
    }
    
    public int generateLevel() {
        int lvl = 0;
        while (lvl < maxLevel && random.nextDouble() < p) {
            lvl++;
        }
        return lvl;
    }
    
    public int[] simulateDistribution(int n) {
        int[] counts = new int[maxLevel + 1];
        for (int i = 0; i < n; i++) {
            counts[generateLevel()]++;
        }
        return counts;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(N)` để sinh `N` phần tử.
- Không gian: `O(MaxLevel)` mảng tần suất.

## 4. Implement Delete in Skip List
**Đề bài chi tiết:**
Triển khai hàm Delete cho Skip List sao cho nếu có nhiều phần tử trùng lặp (duplicates), nó chỉ xóa đi MỘT phần tử đầu tiên tìm được.

**Phân tích thuật toán:**
Hàm `erase` của chuẩn Skiplist vốn dĩ đã tìm phần tử đầu tiên (vì `val < num` mới tiếp tục). Sau khi tìm vị trí update, cập nhật con trỏ `update[i].forward[i]` để trượt qua nó.

**Mã nguồn Java:**
```java
class SkipListEraseNode {
    static class Node {
        int val;
        Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level + 1]; }
    }
    
    public boolean deleteDuplicate(Node head, int currentLevel, int num) {
        Node[] update = new Node[currentLevel + 1];
        Node curr = head;
        
        for (int i = currentLevel; i >= 0; i--) {
            while (curr.forward[i] != null && curr.forward[i].val < num) {
                curr = curr.forward[i];
            }
            update[i] = curr;
        }
        
        curr = curr.forward[0];
        
        if (curr != null && curr.val == num) {
            for (int i = 0; i <= currentLevel; i++) {
                if (update[i].forward[i] != curr) break;
                update[i].forward[i] = curr.forward[i];
            }
            return true;
        }
        return false;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(log N)` Expected.
- Không gian: `O(log N)` do mảng `update`.

## 5. Range Query on Skip List
**Đề bài chi tiết:**
Cho một mảng các phần tử `start` và `end`. In ra tất cả các giá trị của các node trong Skip List nằm trong đoạn `[start, end]`.

**Phân tích thuật toán:**
Thay vì dùng Iterator toàn bộ, sử dụng tính chất search để nhảy nhanh đến node `start` (hoặc node đầu tiên `>= start`), sau đó tiến hành duyệt tuyến tính ở tầng 0 cho đến khi vượt qua `end`.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

class SkipListRangeQuery {
    static class Node {
        int val;
        Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level+1]; }
    }
    
    public List<Integer> query(Node head, int level, int start, int end) {
        List<Integer> result = new ArrayList<>();
        Node curr = head;
        
        // Find position of start
        for (int i = level; i >= 0; i--) {
            while (curr.forward[i] != null && curr.forward[i].val < start) {
                curr = curr.forward[i];
            }
        }
        
        curr = curr.forward[0]; // first node >= start
        
        // Iterate on level 0
        while (curr != null && curr.val <= end) {
            result.add(curr.val);
            curr = curr.forward[0];
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(log N + K)` với `K` là số phần tử trong khoảng truy vấn.
- Không gian: `O(K)` để lưu trữ kết quả.

## 6. Merge Two Skip Lists
**Đề bài chi tiết:**
Gộp (Merge) hai Skip List lại thành một cấu trúc duy nhất sao cho vẫn đảm bảo tính chất tăng dần. Cho đơn giản, chỉ cần kết nối các node ở tầng 0 như Merge Two Sorted Lists.

**Phân tích thuật toán:**
Duyệt qua tầng 0 của hai cấu trúc, sử dụng con trỏ thay thế. Các level phía trên sẽ bị phá vỡ nên cần thiết lập lại level ngẫu nhiên cho nút hợp nhất hoặc tái xây dựng. Để tối giản ở tầng 0:

**Mã nguồn Java:**
```java
class SkipListMerge {
    static class Node {
        int val;
        Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level+1]; }
    }
    
    public Node mergeLevel0(Node l1, Node l2) {
        Node dummy = new Node(-1, 0);
        Node curr = dummy;
        
        // head skip list usually val is dummy/negative
        l1 = l1.forward[0];
        l2 = l2.forward[0];
        
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                curr.forward[0] = l1;
                l1 = l1.forward[0];
            } else {
                curr.forward[0] = l2;
                l2 = l2.forward[0];
            }
            curr = curr.forward[0];
        }
        
        if (l1 != null) curr.forward[0] = l1;
        if (l2 != null) curr.forward[0] = l2;
        
        Node newHead = new Node(-1, 0);
        newHead.forward[0] = dummy.forward[0];
        return newHead;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(N + M)` để duyệt tầng 0.
- Không gian: `O(1)` con trỏ tham chiếu.

## 7. Find kth Smallest Element in Skip List
**Đề bài chi tiết:**
Tìm phần tử nhỏ thứ `k` trong Skip List. Lưu ý Skip List cơ bản không lưu rank (span), nhưng bài tập này yêu cầu đếm bằng duyệt tuyến tính trên tầng 0.

**Phân tích thuật toán:**
Mỗi level 0 chứa danh sách đầy đủ. Bắt đầu từ node tiếp theo của Head, tiến lên phía trước `k-1` bước để tìm node thứ `k`.

**Mã nguồn Java:**
```java
class SkipListKthElement {
    static class Node {
        int val;
        Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level+1]; }
    }
    
    public int findKth(Node head, int k) {
        Node curr = head.forward[0];
        int count = 1;
        
        while (curr != null && count < k) {
            curr = curr.forward[0];
            count++;
        }
        
        if (curr != null && count == k) {
            return curr.val;
        }
        return -1; // Not enough elements
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(k)`.
- Không gian: `O(1)`.

## 8. Concurrent Skip List simulation (Basic thread safety)
**Đề bài chi tiết:**
Tạo một cấu trúc Skip List bọc lại bằng Khóa `ReentrantLock` cho thao tác đọc / ghi đồng thời cơ bản.

**Phân tích thuật toán:**
Đồng bộ hóa các phương thức bằng Java Lock API. Đây là coarse-grained locking, tuy hiệu suất kém hơn CAS không khóa, nhưng là minh họa về Thread Safety.

**Mã nguồn Java:**
```java
import java.util.concurrent.locks.ReentrantReadWriteLock;
import java.util.Random;

class ThreadSafeSkipList {
    private static final int MAX_LEVEL = 16;
    private class Node {
        int val; Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level + 1]; }
    }
    
    private Node head = new Node(-1, MAX_LEVEL);
    private int level = 0;
    private Random rand = new Random();
    private final ReentrantReadWriteLock lock = new ReentrantReadWriteLock();
    
    public boolean search(int target) {
        lock.readLock().lock();
        try {
            Node curr = head;
            for (int i = level; i >= 0; i--) {
                while (curr.forward[i] != null && curr.forward[i].val < target) {
                    curr = curr.forward[i];
                }
            }
            return curr.forward[0] != null && curr.forward[0].val == target;
        } finally {
            lock.readLock().unlock();
        }
    }
    
    public void add(int target) {
        lock.writeLock().lock();
        try {
            // Implementation similar to standard add
            // omitted full code for brevity, logic remains identical
            // but under write lock scope
        } finally {
            lock.writeLock().unlock();
        }
    }
}
```

**Độ phức tạp:**
- Thời gian: Lock overhead + `O(log N)`.
- Không gian: `O(N)`.

## 9. Memory-optimized Skip List array representation
**Đề bài chi tiết:**
Thay vì dùng mảng đối tượng `forward` phức tạp trong Node, chuyển về biểu diễn 1 mảng lớn 1D hoặc danh sách song song để tiết kiệm object headers.

**Phân tích thuật toán:**
Thiết kế mảng các node ID, kèm theo mảng level pointers để mô phỏng phân cấp bộ nhớ thân thiện với Cache hơn. (Chỉ tạo thiết kế Interface).

**Mã nguồn Java:**
```java
class CompactSkipList {
    private int[] values;
    private int[][] nextPointers;
    private int headIndex;
    private int size;
    private int capacity;
    
    public CompactSkipList(int capacity, int maxLevel) {
        this.capacity = capacity;
        this.values = new int[capacity];
        this.nextPointers = new int[capacity][maxLevel];
        this.headIndex = 0;
        this.size = 1; // 0 index for head
    }
    
    // allocate an index for new node
    public int allocateNode(int val, int level) {
        if (size >= capacity) throw new RuntimeException("List full");
        int idx = size++;
        values[idx] = val;
        // nextPointers[idx] initialized to 0
        return idx;
    }
}
```

**Độ phức tạp:**
- Thời gian: Khởi tạo mảng tĩnh `O(N)`.
- Không gian: Giảm overhead JVM header. `O(N * maxLevel)`.

## 10. Compare Skip List vs AVL Tree performance
**Đề bài chi tiết:**
Viết một script ngắn cấu trúc quá trình Benchmark để đánh giá thời gian chạy của SkipList và TreeSet.

**Phân tích thuật toán:**
Đo thời gian ngẫu nhiên chèn 1,000,000 số ngẫu nhiên vào `ConcurrentSkipListSet` so với `TreeSet` đồng bộ hóa bằng `Collections.synchronizedSet()`.

**Mã nguồn Java:**
```java
import java.util.*;
import java.util.concurrent.*;

class BenchmarkSkipList {
    public static void main(String[] args) {
        int n = 1000000;
        Random rand = new Random();
        List<Integer> inputs = new ArrayList<>(n);
        for (int i = 0; i < n; i++) inputs.add(rand.nextInt());
        
        Set<Integer> skipList = new ConcurrentSkipListSet<>();
        Set<Integer> treeSet = Collections.synchronizedSet(new TreeSet<>());
        
        long start = System.currentTimeMillis();
        for (int num : inputs) skipList.add(num);
        long end = System.currentTimeMillis();
        System.out.println("SkipList Insert Time: " + (end - start) + "ms");
        
        start = System.currentTimeMillis();
        for (int num : inputs) treeSet.add(num);
        end = System.currentTimeMillis();
        System.out.println("Synchronized TreeSet Insert Time: " + (end - start) + "ms");
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(N log N)` để chèn.
- Không gian: `O(N)` dữ liệu.

## 11. Backward Pointers in Skip List
**Đề bài chi tiết:**
Bổ sung con trỏ `prev` ở tầng 0 cho cấu trúc Skip List cơ bản để hỗ trợ duyệt ngược chiều. Triển khai thêm phương thức `printDescending()` để in ra toàn bộ danh sách từ lớn đến bé.

**Phân tích thuật toán:**
Trong quá trình thêm và xóa node, ta cần cập nhật thêm một liên kết đôi (doubly-linked) ở level 0 (`forward[0]` và `prev`).
Khi thêm `newNode`, `newNode.prev` trỏ tới `update[0]`, và node liền sau `newNode` sẽ có `prev` trỏ về `newNode`. Tương tự với thao tác xoá.

**Mã nguồn Java:**
```java
import java.util.Random;

class SkipListBackward {
    private static final int MAX_LEVEL = 16;
    private static final double P = 0.5;
    
    class Node {
        int val;
        Node[] forward;
        Node prev;
        
        public Node(int val, int level) {
            this.val = val;
            this.forward = new Node[level + 1];
        }
    }
    
    private Node head;
    private Node tail;
    private int level;
    private Random random;
    
    public SkipListBackward() {
        head = new Node(-1, MAX_LEVEL);
        tail = head;
        level = 0;
        random = new Random();
    }
    
    private int randomLevel() {
        int lvl = 0;
        while (lvl < MAX_LEVEL && random.nextDouble() < P) {
            lvl++;
        }
        return lvl;
    }
    
    public void add(int num) {
        Node[] update = new Node[MAX_LEVEL + 1];
        Node curr = head;
        
        for (int i = level; i >= 0; i--) {
            while (curr.forward[i] != null && curr.forward[i].val < num) {
                curr = curr.forward[i];
            }
            update[i] = curr;
        }
        
        int newLevel = randomLevel();
        if (newLevel > level) {
            for (int i = level + 1; i <= newLevel; i++) {
                update[i] = head;
            }
            level = newLevel;
        }
        
        Node newNode = new Node(num, newLevel);
        for (int i = 0; i <= newLevel; i++) {
            newNode.forward[i] = update[i].forward[i];
            update[i].forward[i] = newNode;
        }
        
        newNode.prev = update[0];
        if (newNode.forward[0] != null) {
            newNode.forward[0].prev = newNode;
        } else {
            tail = newNode;
        }
    }
    
    public void printDescending() {
        Node curr = tail;
        while (curr != head) {
            System.out.print(curr.val + " ");
            curr = curr.prev;
        }
        System.out.println();
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(log N)` cho Add/Search/Delete. `O(N)` cho in danh sách lùi.
- Không gian: `O(N)`.

## 12. Rank / Index Tracking Skip List
**Đề bài chi tiết:**
Thêm trường `span` (hoặc khoảng cách) vào mỗi liên kết để có thể tìm phần tử thứ `K` trong thời gian `O(log N)` thay vì phải duyệt tuần tự `O(K)`.

**Phân tích thuật toán:**
Mỗi phần tử trong mảng `forward` của Node không chỉ lưu con trỏ trỏ đến Node tiếp theo ở level đó, mà còn lưu khoảng cách (số bước nhảy ở level 0) đến Node đó. Khi tìm kiếm vị trí thứ `K`, ta cộng dồn các khoảng cách đi qua; nếu tổng khoảng cách ở mức hiện tại lớn hơn `K`, ta rớt xuống mức thấp hơn.

**Mã nguồn Java:**
```java
import java.util.Random;

class SkipListRank {
    private static final int MAX_LEVEL = 16;
    private static final double P = 0.5;
    
    class Node {
        int val;
        Node[] forward;
        int[] span;
        
        public Node(int val, int level) {
            this.val = val;
            this.forward = new Node[level + 1];
            this.span = new int[level + 1];
        }
    }
    
    private Node head;
    private int level;
    private Random random;
    private int size;
    
    public SkipListRank() {
        head = new Node(-1, MAX_LEVEL);
        level = 0;
        size = 0;
        random = new Random();
    }
    
    public int findKth(int k) {
        if (k < 1 || k > size) return -1;
        
        Node curr = head;
        int traversed = 0;
        
        for (int i = level; i >= 0; i--) {
            while (curr.forward[i] != null && traversed + curr.span[i] <= k) {
                traversed += curr.span[i];
                curr = curr.forward[i];
            }
        }
        
        return curr.val;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(log N)` để truy cập theo index.
- Không gian: `O(N)`.

## 13. Skip List to BST
**Đề bài chi tiết:**
Chuyển đổi một Skip List (sử dụng các liên kết ở `tầng 0`) thành một cây nhị phân tìm kiếm (BST) có độ cao cân bằng tối ưu.

**Phân tích thuật toán:**
Các phần tử ở `tầng 0` của Skip List luôn có thứ tự tăng dần. Bài toán này tương đương "Chuyển danh sách liên kết đã sắp xếp thành BST cân bằng". Có thể dùng cách tiếp cận chia để trị (Divide and Conquer) và tạo cây theo thứ tự Inorder traversal để tối ưu `O(N)` thời gian.

**Mã nguồn Java:**
```java
class SkipListToBST {
    static class Node {
        int val;
        Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level + 1]; }
    }
    
    static class TreeNode {
        int val;
        TreeNode left, right;
        TreeNode(int val) { this.val = val; }
    }
    
    private Node current;
    
    public TreeNode sortedListToBST(Node head) {
        if (head == null || head.forward[0] == null) return null;
        current = head.forward[0];
        
        int count = 0;
        Node temp = current;
        while (temp != null) {
            count++;
            temp = temp.forward[0];
        }
        
        return constructBST(0, count - 1);
    }
    
    private TreeNode constructBST(int start, int end) {
        if (start > end) return null;
        
        int mid = start + (end - start) / 2;
        TreeNode left = constructBST(start, mid - 1);
        
        TreeNode root = new TreeNode(current.val);
        root.left = left;
        current = current.forward[0];
        
        root.right = constructBST(mid + 1, end);
        return root;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(N)`.
- Không gian: `O(log N)` cho ngăn xếp đệ quy.

## 14. Serialize and Deserialize Skip List
**Đề bài chi tiết:**
Lưu trữ toàn bộ dữ liệu (Serialize) của Skip List thành một chuỗi văn bản (String), sau đó khôi phục lại (Deserialize) nguyên vẹn cả các node và phân cấp (level) của chúng.

**Phân tích thuật toán:**
Duyệt mảng một chiều ở `level 0`. Mỗi phần tử ta in ra định dạng `Giá_trị,Chiều_cao;`.
Khi khôi phục, chẻ chuỗi bằng ký hiệu `;` rồi đọc lần lượt để tạo node. Giống như quá trình `add` nhưng không dùng thuật toán random vì đã biết trước `level`. Cần duy trì mảng `update` để gán lại reference đúng cách.

**Mã nguồn Java:**
```java
class SkipListSerialization {
    static class Node {
        int val;
        Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level + 1]; }
    }
    
    private static final int MAX_LEVEL = 16;
    
    public String serialize(Node head) {
        StringBuilder sb = new StringBuilder();
        Node curr = head.forward[0];
        while (curr != null) {
            sb.append(curr.val).append(",").append(curr.forward.length - 1).append(";");
            curr = curr.forward[0];
        }
        return sb.toString();
    }
    
    public Node deserialize(String data) {
        Node head = new Node(-1, MAX_LEVEL);
        if (data == null || data.isEmpty()) return head;
        
        Node[] update = new Node[MAX_LEVEL + 1];
        for (int i = 0; i <= MAX_LEVEL; i++) update[i] = head;
        
        String[] nodes = data.split(";");
        for (String nodeStr : nodes) {
            if (nodeStr.isEmpty()) continue;
            String[] parts = nodeStr.split(",");
            int val = Integer.parseInt(parts[0]);
            int level = Integer.parseInt(parts[1]);
            
            Node newNode = new Node(val, level);
            for (int i = 0; i <= level; i++) {
                update[i].forward[i] = newNode;
                update[i] = newNode;
            }
        }
        return head;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(N)` để duyệt tất cả các Node ở cả Serialize và Deserialize.
- Không gian: `O(N)` cho chuỗi dữ liệu đầu ra.

## 15. Find Closest Node
**Đề bài chi tiết:**
Tìm giá trị trong Skip List có khoảng cách tuyệt đối gần nhất với tham số `target`. Nếu có hai phần tử cách đều `target`, trả về phần tử nhỏ hơn.

**Phân tích thuật toán:**
Dùng Search để tìm được node lớn nhất nhưng nhỏ hơn hoặc bằng `target` (node `A`). `forward[0]` của `A` sẽ là node nhỏ nhất lớn hơn `target` (node `B`).
Ta chỉ việc tính khoảng cách từ `target` đến giá trị của `A` và `B`, so sánh và đưa ra kết quả phù hợp.

**Mã nguồn Java:**
```java
class SkipListClosestNode {
    static class Node {
        int val;
        Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level + 1]; }
    }
    
    public int findClosest(Node head, int level, int target) {
        Node curr = head;
        for (int i = level; i >= 0; i--) {
            while (curr.forward[i] != null && curr.forward[i].val < target) {
                curr = curr.forward[i];
            }
        }
        
        Node next = curr.forward[0];
        
        if (curr == head) {
            return next != null ? next.val : -1;
        }
        if (next == null) {
            return curr.val;
        }
        
        int diff1 = Math.abs(curr.val - target);
        int diff2 = Math.abs(next.val - target);
        
        if (diff1 <= diff2) {
            return curr.val;
        } else {
            return next.val;
        }
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(log N)` cho quá trình tìm kiếm.
- Không gian: `O(1)`.

## 16. Skip List Floor and Ceiling
**Đề bài chi tiết:**
Cài đặt hàm `floor(target)` để trả về giá trị lớn nhất `<= target` và hàm `ceiling(target)` để trả về giá trị nhỏ nhất `>= target`.

**Phân tích thuật toán:**
Hàm `floor`: Tiến hành search và liên tục nhảy cho đến khi phát hiện node lớn hơn `target`, khi đó node hiện tại (nếu khác `head`) chính là giá trị cần tìm.
Hàm `ceiling`: Lấy `forward[0]` của node vừa tìm được trong `floor`, đây sẽ là điểm gần nhất ở phía bên phải.

**Mã nguồn Java:**
```java
class SkipListFloorCeiling {
    static class Node {
        int val;
        Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level + 1]; }
    }
    
    public Integer floor(Node head, int level, int target) {
        Node curr = head;
        for (int i = level; i >= 0; i--) {
            while (curr.forward[i] != null && curr.forward[i].val <= target) {
                curr = curr.forward[i];
            }
        }
        return curr == head ? null : curr.val;
    }
    
    public Integer ceiling(Node head, int level, int target) {
        Node curr = head;
        for (int i = level; i >= 0; i--) {
            while (curr.forward[i] != null && curr.forward[i].val < target) {
                curr = curr.forward[i];
            }
        }
        curr = curr.forward[0];
        return curr == null ? null : curr.val;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(log N)` cho cả hai thao tác.
- Không gian: `O(1)`.

## 17. Intersection of Two Skip Lists
**Đề bài chi tiết:**
Liệt kê tất cả các phần tử cùng xuất hiện trong cả hai Skip List. Yêu cầu giới hạn thời gian tuyến tính `O(N + M)`.

**Phân tích thuật toán:**
Bỏ qua các tầng trên, ta chỉ cần sử dụng tính chất danh sách liên kết tăng dần tại tầng 0 (`level 0`). Đặt hai con trỏ vào 2 head của danh sách, tiến hành đẩy lên theo phương pháp Two Pointers: giá trị bên nào nhỏ hơn thì tiến bên đó lên trước, nếu bằng nhau thì đưa vào mảng kết quả rồi tiến cả hai.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

class SkipListIntersection {
    static class Node {
        int val;
        Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level + 1]; }
    }
    
    public List<Integer> getIntersection(Node head1, Node head2) {
        List<Integer> result = new ArrayList<>();
        Node p1 = head1.forward[0];
        Node p2 = head2.forward[0];
        
        while (p1 != null && p2 != null) {
            if (p1.val == p2.val) {
                result.add(p1.val);
                p1 = p1.forward[0];
                p2 = p2.forward[0];
            } else if (p1.val < p2.val) {
                p1 = p1.forward[0];
            } else {
                p2 = p2.forward[0];
            }
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(N + M)` vì phải quét hết cả hai mảng tại level 0.
- Không gian: `O(K)` cho phần tử giao nhau để lưu trữ kết quả.

## 18. Build Skip List from Array
**Đề bài chi tiết:**
Tạo Skip List từ một mảng số nguyên đã sắp xếp. Tránh sử dụng hàm `add` nhiều lần (làm cho độ phức tạp là `O(N log N)`) bằng cách tiếp cận theo Bulk Loading.

**Phân tích thuật toán:**
Duy trì mảng `update` đại diện cho các "con trỏ tận cùng" ở từng level. Với mỗi giá trị mảng đầu vào, gán một số level ngẫu nhiên, khởi tạo Node mới và đấu nối nhanh trực tiếp từ `update` vào `newNode`, sau đó gán lại `update` là `newNode`.

**Mã nguồn Java:**
```java
import java.util.Random;

class BuildSkipListFromArray {
    private static final int MAX_LEVEL = 16;
    private static final double P = 0.5;
    
    static class Node {
        int val;
        Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level + 1]; }
    }
    
    private Random random = new Random();
    
    private int randomLevel() {
        int lvl = 0;
        while (lvl < MAX_LEVEL && random.nextDouble() < P) {
            lvl++;
        }
        return lvl;
    }
    
    public Node build(int[] arr) {
        Node head = new Node(-1, MAX_LEVEL);
        Node[] update = new Node[MAX_LEVEL + 1];
        for (int i = 0; i <= MAX_LEVEL; i++) update[i] = head;
        
        for (int num : arr) {
            int level = randomLevel();
            Node newNode = new Node(num, level);
            for (int i = 0; i <= level; i++) {
                update[i].forward[i] = newNode;
                update[i] = newNode;
            }
        }
        return head;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(N)` chỉ chạy 1 lần qua các phần tử.
- Không gian: `O(N)`.

## 19. Deterministic Skip List (1-2-3 Skip List)
**Đề bài chi tiết:**
Viết bộ khung khởi tạo Deterministic Skip List (ví dụ 1-2-3 Skip List) - một biến thể phi xác suất nơi số node ở mỗi tầng tuân theo luật bù trừ chặt chẽ như B-Tree, qua đó loại bỏ việc tung đồng xu.

**Phân tích thuật toán:**
Sử dụng mảng 2 chiều bằng tham chiếu `down` và `right` để thể hiện mô hình thay cho cấu trúc mảng liền kề vì việc thêm phần tử có thể đẩy Node "trồi lên" giống nút cha của B-Tree. Khi có 3 node ở cùng 1 đường chân trời, tiến hành promotion.

**Mã nguồn Java:**
```java
class DeterministicSkipListDummy {
    static class Node {
        int val;
        Node down, right; 
        Node(int val) { this.val = val; }
    }
    
    private Node head; 
    
    public DeterministicSkipListDummy() {
        head = new Node(Integer.MIN_VALUE);
        head.right = new Node(Integer.MAX_VALUE);
        head.down = new Node(Integer.MIN_VALUE);
        head.down.right = new Node(Integer.MAX_VALUE);
    }
    
    public void insert(int val) {
        // Mô phỏng Deterministic Skip List:
        // Quá trình sẽ đi tìm vị trí ở tầng thấp nhất, nếu khoảng trống 
        // vượt quá 3 node (do thêm 1), thì bốc node giữa thăng hạng (promote) 
        // lên tầng trên để duy trì cấu trúc độ phức tạp giới hạn cứng O(log N).
        Node curr = head;
        while (curr != null) {
            if (curr.right != null && curr.right.val < val) {
                curr = curr.right;
            } else {
                if (curr.down == null) {
                    Node newNode = new Node(val);
                    newNode.right = curr.right;
                    curr.right = newNode;
                    break;
                }
                curr = curr.down;
            }
        }
    }
}
```

**Độ phức tạp:**
- Thời gian: Kì vọng `O(log N)` cho chèn xóa tuyệt đối (worst-case).
- Không gian: `O(N)`.

## 20. Sublist Extraction
**Đề bài chi tiết:**
Tách hoặc trích xuất (Extract) tất cả các node có giá trị trong đoạn `[L, R]` ra thành một List độc lập để xử lý riêng (giống SubMap).

**Phân tích thuật toán:**
Sử dụng hàm tìm kiếm của cấu trúc để đáp ngay xuống node nhỏ nhất `>= L`. Sau đó lặp lại qua các `forward[0]` cho đến khi vượt ngưỡng `R`. Mọi giá trị lấy được bỏ vào mảng phụ.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

class SublistExtraction {
    static class Node {
        int val;
        Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level + 1]; }
    }
    
    public List<Integer> extractSublist(Node head, int level, int L, int R) {
        List<Integer> sublist = new ArrayList<>();
        Node curr = head;
        
        for (int i = level; i >= 0; i--) {
            while (curr.forward[i] != null && curr.forward[i].val < L) {
                curr = curr.forward[i];
            }
        }
        
        curr = curr.forward[0];
        
        while (curr != null && curr.val <= R) {
            sublist.add(curr.val);
            curr = curr.forward[0];
        }
        
        return sublist;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(log N + K)` (với `K` là số phần tử trong khoảng).
- Không gian: `O(K)` cho mảng phụ được kết xuất.

## 21. Multi-Dimensional Skip List (Quad Skip List concept)
**Đề bài chi tiết:**
Mô phỏng một cấu trúc Multi-Dimensional Skip List (hoặc 2D Skip List) cơ bản. Mỗi node không chỉ nhảy tới trước (`forward`) mà còn nhảy xuống dưới (`down`) trong một không gian toạ độ lưới 2 chiều để phục vụ cho các truy vấn không gian (spatial queries).

**Phân tích thuật toán:**
Một node trong không gian 2D cần 4 hướng kết nối, hoặc lưu các mảng con trỏ cho 2 chiều X và Y. Để đơn giản hoá bài tập, ta xây dựng class Node chứa mảng `forwardX` và `forwardY`. Phương thức chèn hoặc tìm kiếm sẽ đi theo chiều X trước rồi rẽ sang chiều Y.

**Mã nguồn Java:**
```java
class SkipList2D {
    private static final int MAX_LEVEL = 16;
    
    class Node {
        int x, y;
        Node[] forwardX;
        Node[] forwardY;
        
        public Node(int x, int y, int level) {
            this.x = x;
            this.y = y;
            this.forwardX = new Node[level + 1];
            this.forwardY = new Node[level + 1];
        }
    }
    
    private Node head;
    
    public SkipList2D() {
        head = new Node(Integer.MIN_VALUE, Integer.MIN_VALUE, MAX_LEVEL);
    }
    
    // Hàm search cơ bản tìm node có tọa độ (targetX, targetY)
    public boolean search(int targetX, int targetY) {
        Node curr = head;
        // Đi theo chiều X trước
        for (int i = MAX_LEVEL - 1; i >= 0; i--) {
            while (curr.forwardX[i] != null && curr.forwardX[i].x < targetX) {
                curr = curr.forwardX[i];
            }
        }
        if (curr.forwardX[0] != null && curr.forwardX[0].x == targetX) {
            curr = curr.forwardX[0];
        }
        
        // Từ node đó, đi theo chiều Y
        for (int i = MAX_LEVEL - 1; i >= 0; i--) {
            while (curr.forwardY[i] != null && curr.forwardY[i].y < targetY) {
                curr = curr.forwardY[i];
            }
        }
        
        curr = curr.forwardY[0];
        return curr != null && curr.x == targetX && curr.y == targetY;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(log N + log M)` với N, M là số phần tử theo mỗi chiều.
- Không gian: `O(N * M)` cho số node tổng cộng.

## 22. Delete All Duplicates
**Đề bài chi tiết:**
Viết lại thuật toán `erase` của Skip List. Trong trường hợp có nhiều bản sao (duplicates) của cùng một giá trị `target`, thuật toán phải xoá toàn bộ thay vì chỉ xoá phần tử đầu tiên tìm thấy.

**Phân tích thuật toán:**
Dùng vòng lặp `while` để liên tục kiểm tra node đứng ngay sau vị trí hiện tại ở `level 0` có giá trị bằng `target` hay không. Nếu có, thực hiện xoá và cập nhật lại mảng `update` hoặc thực hiện xoá tuần tự nhiều lần dọc theo `level 0` và vá các liên kết lại.

**Mã nguồn Java:**
```java
class SkipListDeleteAll {
    private static final int MAX_LEVEL = 16;
    
    static class Node {
        int val;
        Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level + 1]; }
    }
    
    private Node head = new Node(-1, MAX_LEVEL);
    private int level = 0;
    
    public boolean eraseAll(int target) {
        Node[] update = new Node[MAX_LEVEL + 1];
        Node curr = head;
        boolean found = false;
        
        for (int i = level; i >= 0; i--) {
            while (curr.forward[i] != null && curr.forward[i].val < target) {
                curr = curr.forward[i];
            }
            update[i] = curr;
        }
        
        curr = curr.forward[0];
        
        // Xoá chừng nào còn gặp target
        while (curr != null && curr.val == target) {
            found = true;
            for (int i = 0; i <= level; i++) {
                if (update[i].forward[i] != curr) break;
                update[i].forward[i] = curr.forward[i];
            }
            curr = curr.forward[0];
        }
        
        // Cập nhật lại level nếu head không còn trỏ đi đâu ở các tầng cao
        while (level > 0 && head.forward[level] == null) {
            level--;
        }
        
        return found;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(log N + K)` với K là số bản sao của phần tử bị xoá.
- Không gian: `O(log N)` cho mảng `update`.

## 23. Probability Tuning Performance
**Đề bài chi tiết:**
Skip List dùng phân phối hình học với xác suất `p`. Nếu `p = 0.5`, trung bình 1 node đi lên là `50%`. Hãy lập trình mô phỏng để tính toán độ sâu lớn nhất (Max Level) đạt được và số node trung bình ở từng tầng khi `N = 1,000,000` với các `p = 0.1`, `0.5`, và `0.9`. Trả về kết quả dưới dạng văn bản.

**Phân tích thuật toán:**
Viết một hàm mô phỏng `generateLevel(p)` và đếm tần suất các level sinh ra. Điều này giúp hiểu ảnh hưởng của xác suất lên bộ nhớ và tốc độ của Skip List.

**Mã nguồn Java:**
```java
import java.util.Random;

class ProbabilityTuning {
    private static final int N = 1_000_000;
    private static final int MAX_ALLOWED_LEVEL = 32;
    private static Random random = new Random();
    
    public static int generateLevel(double p) {
        int lvl = 0;
        while (lvl < MAX_ALLOWED_LEVEL && random.nextDouble() < p) {
            lvl++;
        }
        return lvl;
    }
    
    public static void simulate(double p) {
        int[] counts = new int[MAX_ALLOWED_LEVEL + 1];
        int maxLevelAchieved = 0;
        
        for (int i = 0; i < N; i++) {
            int lvl = generateLevel(p);
            counts[lvl]++;
            if (lvl > maxLevelAchieved) {
                maxLevelAchieved = lvl;
            }
        }
        
        System.out.println("Probability p = " + p);
        System.out.println("Max level achieved: " + maxLevelAchieved);
        System.out.println("Level 0 nodes: " + counts[0]);
        System.out.println("Level 1 nodes: " + counts[1]);
        System.out.println("-------------------------");
    }
    
    public static void main(String[] args) {
        simulate(0.1);
        simulate(0.5);
        simulate(0.9);
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(N)` cho mỗi lần chạy mô phỏng.
- Không gian: `O(1)` mảng nhỏ `MAX_ALLOWED_LEVEL`.

## 24. Top K Frequent Elements with Skip List
**Đề bài chi tiết:**
Cho một danh sách các phần tử, tìm `K` phần tử xuất hiện nhiều nhất. Sử dụng HashMap để đếm tần suất và Skip List (cụ thể là `ConcurrentSkipListSet` hoặc `TreeSet` trong thực tế) để luôn giữ các phần tử sắp xếp theo tần suất xuất hiện. 

**Phân tích thuật toán:**
Ta dùng `Map<Integer, Integer>` lưu `{giá trị -> số lần}`. Sau đó dùng một `SkipListSet` tuỳ chỉnh (với Comparator ưu tiên xếp theo tần suất, sau đó theo giá trị để chống đụng độ) để chứa các entry. Khi danh sách vượt quá kích thước `K`, ta xóa phần tử nhỏ nhất ra khỏi SkipListSet.

**Mã nguồn Java:**
```java
import java.util.*;
import java.util.concurrent.ConcurrentSkipListSet;

class TopKSkipList {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freqMap = new HashMap<>();
        for (int num : nums) {
            freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);
        }
        
        // Sắp xếp tần suất tăng dần, nếu bằng thì xếp giá trị
        Comparator<Integer> comp = (a, b) -> {
            int freqA = freqMap.get(a);
            int freqB = freqMap.get(b);
            if (freqA != freqB) return Integer.compare(freqA, freqB);
            return Integer.compare(a, b);
        };
        
        ConcurrentSkipListSet<Integer> skipList = new ConcurrentSkipListSet<>(comp);
        
        for (int key : freqMap.keySet()) {
            skipList.add(key);
            if (skipList.size() > k) {
                skipList.pollFirst(); // Loại bỏ phần tử có tần suất nhỏ nhất
            }
        }
        
        int[] result = new int[k];
        int idx = k - 1;
        for (int num : skipList) {
            result[idx--] = num;
        }
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(N log K)` cho việc duyệt và đưa vào Skip List.
- Không gian: `O(N)` cho HashMap và `O(K)` cho Skip List.

## 25. Memory Leak Detector in Custom Skip List
**Đề bài chi tiết:**
Viết một phương thức `assertNoLeaks(int target)` để sau khi xoá một Node có giá trị `target`, đảm bảo rằng Node đó không còn bất kì liên kết nào được trỏ tới từ các Node khác trong toàn bộ Skip List.

**Phân tích thuật toán:**
Do Garbage Collector của Java tự dọn dẹp các node không còn đường dẫn tới, leak thường chỉ xảy ra khi ta quên gỡ một tham chiếu (ví dụ ở tầng cao). Hàm Assertion sẽ duyệt toàn bộ Skip List ở TẤT CẢ các level để kiểm tra xem có node nào đang `forward[i]` tới một node mang giá trị `target` đã bị xoá hay không.

**Mã nguồn Java:**
```java
class SkipListLeakDetection {
    static class Node {
        int val;
        Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level + 1]; }
    }
    
    private Node head;
    private int currentLevel;
    
    // Giả sử hàm erase() đã được gọi để xóa target
    
    public boolean assertNoLeaks(int deletedTarget) {
        // Duyệt mọi level từ cao xuống thấp
        for (int i = currentLevel; i >= 0; i--) {
            Node curr = head;
            while (curr.forward[i] != null) {
                if (curr.forward[i].val == deletedTarget) {
                    System.err.println("Memory Leak Detected at Level " + i);
                    return false; // Phát hiện tham chiếu rác
                }
                curr = curr.forward[i];
            }
        }
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(N)` trong trường hợp xấu nhất (vì duyệt trần tục qua level 0 đã là `O(N)`).
- Không gian: `O(1)`.

## 26. Count Nodes in Interval
**Đề bài chi tiết:**
Đếm số lượng phần tử thuộc khoảng `[A, B]` nằm trong Skip List. Thay vì duyệt tuyến tính qua tất cả các phần tử, ta hãy kết hợp mảng `span` (của Bài 12) để đạt được thời gian `O(log N)`. Tuy nhiên, để minh họa một hàm đếm đơn giản, ta sẽ chỉ duyệt `level 0` từ `A` đến `B`.

**Phân tích thuật toán:**
Dùng hàm tìm kiếm đi thẳng đến node đầu tiên `>= A`. Bắt đầu biến đếm `count = 0`. Tiếp tục tiến dọc theo `level 0` và tăng đếm cho tới khi giá trị node hiện tại `> B` hoặc đụng `null`.

**Mã nguồn Java:**
```java
class SkipListCountInterval {
    static class Node {
        int val;
        Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level + 1]; }
    }
    
    public int countRange(Node head, int level, int A, int B) {
        Node curr = head;
        
        for (int i = level; i >= 0; i--) {
            while (curr.forward[i] != null && curr.forward[i].val < A) {
                curr = curr.forward[i];
            }
        }
        
        curr = curr.forward[0];
        int count = 0;
        
        while (curr != null && curr.val <= B) {
            count++;
            curr = curr.forward[0];
        }
        
        return count;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(log N + K)` (với `K` là số lượng phần tử lọt vào khoảng đếm).
- Không gian: `O(1)`.

## 27. Concurrent CAS Node Insertion
**Đề bài chi tiết:**
Mô phỏng thao tác chèn Node ở một Skip List đồng thời cao (Lock-free) bằng cách sử dụng `AtomicReference` và vòng lặp Compare-And-Swap (CAS). Lược bỏ thao tác multi-level, chỉ cài đặt thêm node ở `level 0`.

**Phân tích thuật toán:**
Trong cấu trúc lock-free, việc cập nhật con trỏ tiếp theo không dùng Lock mà dùng vòng lặp vô tận: đọc con trỏ hiện tại, chuẩn bị node mới trỏ vào con trỏ đó, rồi dùng CAS để đổi con trỏ cũ sang node mới. Nếu CAS thất bại (do luồng khác chen ngang), lặp lại tiến trình.

**Mã nguồn Java:**
```java
import java.util.concurrent.atomic.AtomicReference;

class LockFreeSkipListDummy {
    static class Node {
        int val;
        AtomicReference<Node> next;
        
        Node(int val) {
            this.val = val;
            this.next = new AtomicReference<>(null);
        }
    }
    
    private Node head = new Node(Integer.MIN_VALUE);
    
    // Thêm Node lock-free ở tầng 0 (Minh hoạ cấu trúc danh sách liên kết đơn lock-free)
    public void addLevel0(int val) {
        Node newNode = new Node(val);
        while (true) {
            Node curr = head;
            Node nextNode = curr.next.get();
            
            // Tìm vị trí chèn
            while (nextNode != null && nextNode.val < val) {
                curr = nextNode;
                nextNode = curr.next.get();
            }
            
            // Trỏ next của node mới tới nextNode
            newNode.next.set(nextNode);
            
            // Dùng CAS để đổi next của curr thành newNode
            if (curr.next.compareAndSet(nextNode, newNode)) {
                break; // Thành công
            }
            // Thất bại thì vòng lặp while(true) sẽ thử lại từ đầu
        }
    }
}
```

**Độ phức tạp:**
- Thời gian: Kì vọng `O(N)` do phải dò từ đầu, `O(1)` cho thao tác chèn. Nếu áp dụng đủ multi-level sẽ là `O(log N)`.
- Không gian: `O(1)`.

## 28. Skip List Iterators
**Đề bài chi tiết:**
Hiện thực giao diện `Iterator<Integer>` cho Skip List để hỗ trợ cú pháp `for-each` trong Java. Iterator cần cung cấp 2 phương thức chính là `hasNext()` và `next()`.

**Phân tích thuật toán:**
Iterator chỉ cần lưu trạng thái con trỏ hiện tại của `level 0`. Khi khởi tạo, nó được đặt tại `head.forward[0]`. Mỗi khi gọi `next()`, nó ghi nhớ giá trị hiện tại, tiến con trỏ lên một bước và trả về giá trị đã ghi nhớ.

**Mã nguồn Java:**
```java
import java.util.Iterator;
import java.util.NoSuchElementException;

class SkipListIterator implements Iterable<Integer> {
    static class Node {
        int val;
        Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level + 1]; }
    }
    
    private Node head;
    
    public SkipListIterator(Node head) {
        this.head = head;
    }
    
    @Override
    public Iterator<Integer> iterator() {
        return new Iterator<Integer>() {
            private Node current = head.forward[0];
            
            @Override
            public boolean hasNext() {
                return current != null;
            }
            
            @Override
            public Integer next() {
                if (!hasNext()) throw new NoSuchElementException();
                int val = current.val;
                current = current.forward[0];
                return val;
            }
        };
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(1)` mỗi lệnh `next()` hoặc `hasNext()`.
- Không gian: `O(1)` lưu trạng thái.

## 29. First Missing Positive (Skip List Version)
**Đề bài chi tiết:**
Cho một tập hợp các số nguyên đổ vào Skip List. Biến thể thuật toán để nhanh chóng tìm số nguyên dương nhỏ nhất bị thiếu (First Missing Positive). 

**Phân tích thuật toán:**
Thông thường `First Missing Positive` của mảng chưa sắp xếp giải bằng hoán vị index `O(N)`. Vì Skip List đã sắp xếp, ta chỉ việc duyệt từ đầu ở tầng 0, tìm đến node đầu tiên lớn hơn `0`. Sau đó duyệt tuần tự, giá trị kì vọng tiếp theo ban đầu là `1`. Nếu node kế tiếp có giá trị > giá trị kì vọng, thì giá trị kì vọng chính là kết quả. (Bỏ qua duplicates).

**Mã nguồn Java:**
```java
class SkipListFirstMissingPositive {
    static class Node {
        int val;
        Node[] forward;
        Node(int val, int level) { this.val = val; forward = new Node[level + 1]; }
    }
    
    public int firstMissingPositive(Node head, int level) {
        Node curr = head;
        // Chạy nhanh đến node đầu tiên >= 1
        for (int i = level; i >= 0; i--) {
            while (curr.forward[i] != null && curr.forward[i].val < 1) {
                curr = curr.forward[i];
            }
        }
        
        curr = curr.forward[0];
        int expected = 1;
        
        while (curr != null) {
            if (curr.val == expected) {
                expected++;
            } else if (curr.val > expected) {
                return expected;
            }
            curr = curr.forward[0];
        }
        
        return expected;
    }
}
```

**Độ phức tạp:**
- Thời gian: `O(log N + K)` (với `K` là số lượng phần tử dương liên tiếp có mặt).
- Không gian: `O(1)`.

## 30. Implement Redis ZSET (Sorted Set)
**Đề bài chi tiết:**
Cấu trúc Sorted Set (`ZSET`) của Redis dùng một Hash Map kết hợp với Skip List để hỗ trợ việc tìm kiếm/xoá bằng khóa (`O(1)`) và truy xuất/sắp xếp theo điểm số (`score`) trong `O(log N)`. Viết mô hình thu gọn bằng Java.

**Phân tích thuật toán:**
Ta dùng `Map<String, Node>` để tra cứu trực tiếp bằng `Key` (thực hiện truy vấn hoặc xoá). `Node` lưu 2 giá trị là `String key` và `double score`. Các Node được móc nối thành cấu trúc Skip List theo thứ tự điểm số. 

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;
import java.util.Random;

class RedisZSet {
    private static final int MAX_LEVEL = 16;
    private static final double P = 0.5;
    
    class Node {
        String key;
        double score;
        Node[] forward;
        
        public Node(String key, double score, int level) {
            this.key = key;
            this.score = score;
            this.forward = new Node[level + 1];
        }
    }
    
    private Node head;
    private int level;
    private Random random;
    private Map<String, Node> map; // Hỗ trợ tra cứu O(1)
    
    public RedisZSet() {
        head = new Node(null, -Double.MAX_VALUE, MAX_LEVEL);
        level = 0;
        random = new Random();
        map = new HashMap<>();
    }
    
    private int randomLevel() {
        int lvl = 0;
        while (lvl < MAX_LEVEL && random.nextDouble() < P) lvl++;
        return lvl;
    }
    
    // Thêm (hoặc cập nhật) phần tử
    public void zadd(String key, double score) {
        if (map.containsKey(key)) {
            // Nếu tồn tại, xoá phần tử cũ ra khỏi SkipList trước
            zrem(key);
        }
        
        Node[] update = new Node[MAX_LEVEL + 1];
        Node curr = head;
        
        for (int i = level; i >= 0; i--) {
            while (curr.forward[i] != null && 
                   (curr.forward[i].score < score || 
                   (curr.forward[i].score == score && curr.forward[i].key.compareTo(key) < 0))) {
                curr = curr.forward[i];
            }
            update[i] = curr;
        }
        
        int newLevel = randomLevel();
        if (newLevel > level) {
            for (int i = level + 1; i <= newLevel; i++) update[i] = head;
            level = newLevel;
        }
        
        Node newNode = new Node(key, score, newLevel);
        for (int i = 0; i <= newLevel; i++) {
            newNode.forward[i] = update[i].forward[i];
            update[i].forward[i] = newNode;
        }
        
        map.put(key, newNode);
    }
    
    // Xoá phần tử
    public boolean zrem(String key) {
        if (!map.containsKey(key)) return false;
        Node targetNode = map.get(key);
        double score = targetNode.score;
        
        Node[] update = new Node[MAX_LEVEL + 1];
        Node curr = head;
        
        for (int i = level; i >= 0; i--) {
            while (curr.forward[i] != null && 
                   (curr.forward[i].score < score || 
                   (curr.forward[i].score == score && curr.forward[i].key.compareTo(key) < 0))) {
                curr = curr.forward[i];
            }
            update[i] = curr;
        }
        
        curr = curr.forward[0];
        if (curr != null && curr.key.equals(key)) {
            for (int i = 0; i <= level; i++) {
                if (update[i].forward[i] != curr) break;
                update[i].forward[i] = curr.forward[i];
            }
            map.remove(key);
            while (level > 0 && head.forward[level] == null) level--;
            return true;
        }
        return false;
    }
    
    // Trả về điểm của key
    public Double zscore(String key) {
        Node node = map.get(key);
        return node != null ? node.score : null;
    }
}
```

**Độ phức tạp:**
- Thời gian: `zadd` và `zrem` mất `O(log N)`. `zscore` mất `O(1)`.
- Không gian: `O(N)` cho Hash Map và con trỏ cấp.
