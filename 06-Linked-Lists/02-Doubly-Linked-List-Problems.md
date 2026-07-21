# Doubly Linked List Problems (Bài tập Danh Sách Liên Kết Đôi)

## 1. Design Doubly Linked List
**Đề bài chi tiết**: Thiết kế và triển khai một cấu trúc dữ liệu Doubly Linked List hỗ trợ các thao tác: `get(index)`, `addAtHead(val)`, `addAtTail(val)`, `addAtIndex(index, val)`, và `deleteAtIndex(index)`.
**Phân tích thuật toán**: Sử dụng hai Node giả (dummy head và dummy tail) để loại bỏ các điều kiện biên khi chèn và xóa. 
**Mã nguồn Java**:
```java
class MyLinkedList {
    class Node {
        int val;
        Node prev, next;
        Node(int val) { this.val = val; }
    }
    
    Node head, tail;
    int size;

    public MyLinkedList() {
        head = new Node(0);
        tail = new Node(0);
        head.next = tail;
        tail.prev = head;
        size = 0;
    }
    
    public int get(int index) {
        if (index < 0 || index >= size) return -1;
        Node curr = head.next;
        for (int i = 0; i < index; i++) curr = curr.next;
        return curr.val;
    }
    
    public void addAtHead(int val) { addAtIndex(0, val); }
    
    public void addAtTail(int val) { addAtIndex(size, val); }
    
    public void addAtIndex(int index, int val) {
        if (index > size) return;
        if (index < 0) index = 0;
        Node pred = head;
        for (int i = 0; i < index; i++) pred = pred.next;
        Node succ = pred.next;
        Node toAdd = new Node(val);
        toAdd.prev = pred;
        toAdd.next = succ;
        pred.next = toAdd;
        succ.prev = toAdd;
        size++;
    }
    
    public void deleteAtIndex(int index) {
        if (index < 0 || index >= size) return;
        Node pred = head;
        for (int i = 0; i < index; i++) pred = pred.next;
        Node succ = pred.next.next;
        pred.next = succ;
        succ.prev = pred;
        size--;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$ cho get/add/delete theo index, $O(1)$ cho head/tail. Không gian $O(N)$.

---

## 2. LRU Cache Implementation (Using HashMap + DLL)
**Đề bài chi tiết**: Thiết kế cấu trúc dữ liệu LRU (Least Recently Used) Cache có kích thước nhất định, hỗ trợ thao tác `get(key)` và `put(key, value)` trong thời gian O(1).
**Phân tích thuật toán**: Sử dụng `HashMap` lưu key -> Node. Sử dụng DLL với Dummy Head và Tail. Nút sát Head là nút mới truy cập, sát Tail là nút LRU sẽ bị xóa khi đầy.
**Mã nguồn Java**:
```java
import java.util.HashMap;

class LRUCache {
    class Node {
        int key, val;
        Node prev, next;
        Node(int key, int val) { this.key = key; this.val = val; }
    }
    
    private final int capacity;
    private final HashMap<Integer, Node> map;
    private final Node head, tail;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        map = new HashMap<>();
        head = new Node(-1, -1);
        tail = new Node(-1, -1);
        head.next = tail;
        tail.prev = head;
    }
    
    private void removeNode(Node node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }
    
    private void addToHead(Node node) {
        node.next = head.next;
        node.next.prev = node;
        node.prev = head;
        head.next = node;
    }
    
    public int get(int key) {
        if (!map.containsKey(key)) return -1;
        Node node = map.get(key);
        removeNode(node);
        addToHead(node);
        return node.val;
    }
    
    public void put(int key, int value) {
        if (map.containsKey(key)) {
            Node node = map.get(key);
            node.val = value;
            removeNode(node);
            addToHead(node);
        } else {
            if (map.size() == capacity) {
                Node lru = tail.prev;
                map.remove(lru.key);
                removeNode(lru);
            }
            Node newNode = new Node(key, value);
            map.put(key, newNode);
            addToHead(newNode);
        }
    }
}
```
**Độ phức tạp**: Thời gian $O(1)$ cho cả get và put. Không gian $O(K)$ với K là dung lượng cache.

---

## 3. LFU Cache Implementation
**Đề bài chi tiết**: Thiết kế cấu trúc dữ liệu LFU (Least Frequently Used) Cache hỗ trợ `get` và `put` O(1). Nếu nhiều khóa có cùng tần suất, khóa LRU sẽ bị loại bỏ.
**Phân tích thuật toán**: Sử dụng HashMap kết hợp với một Doubly Linked List cho mỗi tần suất truy cập.
**Mã nguồn Java**:
```java
import java.util.HashMap;

class LFUCache {
    class Node {
        int key, val, freq;
        Node prev, next;
        Node(int key, int val) { this.key = key; this.val = val; this.freq = 1; }
    }
    class DLL {
        Node head = new Node(-1, -1), tail = new Node(-1, -1);
        int size = 0;
        DLL() { head.next = tail; tail.prev = head; }
        void add(Node node) {
            node.next = head.next; node.prev = head;
            head.next.prev = node; head.next = node; size++;
        }
        void remove(Node node) {
            node.prev.next = node.next; node.next.prev = node.prev; size--;
        }
        Node removeLast() {
            if (size > 0) { Node tailNode = tail.prev; remove(tailNode); return tailNode; }
            return null;
        }
    }
    
    int capacity, minFreq;
    HashMap<Integer, Node> cache = new HashMap<>();
    HashMap<Integer, DLL> freqMap = new HashMap<>();
    
    public LFUCache(int capacity) {
        this.capacity = capacity; this.minFreq = 0;
    }
    
    public int get(int key) {
        if (!cache.containsKey(key)) return -1;
        Node node = cache.get(key);
        updateNode(node);
        return node.val;
    }
    
    public void put(int key, int value) {
        if (capacity == 0) return;
        if (cache.containsKey(key)) {
            Node node = cache.get(key);
            node.val = value;
            updateNode(node);
        } else {
            if (cache.size() == capacity) {
                DLL minList = freqMap.get(minFreq);
                Node evict = minList.removeLast();
                cache.remove(evict.key);
            }
            Node newNode = new Node(key, value);
            cache.put(key, newNode);
            minFreq = 1;
            freqMap.computeIfAbsent(1, k -> new DLL()).add(newNode);
        }
    }
    
    private void updateNode(Node node) {
        DLL list = freqMap.get(node.freq);
        list.remove(node);
        if (node.freq == minFreq && list.size == 0) minFreq++;
        node.freq++;
        freqMap.computeIfAbsent(node.freq, k -> new DLL()).add(node);
    }
}
```
**Độ phức tạp**: Thời gian $O(1)$ cho cả get và put. Không gian $O(K)$.

---

## 4. Flatten a Multilevel Doubly Linked List
**Đề bài chi tiết**: Cho một DLL có thuộc tính `child`, hãy làm phẳng nó thành DLL 1 tầng.
**Phân tích thuật toán**: Dùng thuật toán DFS. Khi gặp `child`, kết nối Node hiện tại với `child`, sau đó tìm Tail của mức child để nối lại với `next` gốc.
**Mã nguồn Java**:
```java
class Node {
    public int val;
    public Node prev, next, child;
};

class Solution {
    public Node flatten(Node head) {
        if (head == null) return null;
        Node curr = head;
        while (curr != null) {
            if (curr.child != null) {
                Node next = curr.next;
                Node child = flatten(curr.child);
                curr.next = child;
                child.prev = curr;
                curr.child = null;
                
                while (curr.next != null) {
                    curr = curr.next;
                }
                if (next != null) {
                    curr.next = next;
                    next.prev = curr;
                }
            }
            curr = curr.next;
        }
        return head;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(1)$ bỏ qua call stack.

---

## 5. Insert into a Sorted Circular Doubly Linked List
**Đề bài chi tiết**: Chèn một giá trị vào Sorted Circular DLL.
**Phân tích thuật toán**: Tìm vị trí mà giá trị hiện tại <= giá trị mới <= giá trị tiếp theo. Chú ý trường hợp vượt biên (lớn nhất/nhỏ nhất).
**Mã nguồn Java**:
```java
class Solution {
    public Node insert(Node head, int insertVal) {
        Node newNode = new Node(insertVal);
        if (head == null) {
            newNode.next = newNode;
            newNode.prev = newNode;
            return newNode;
        }
        Node curr = head;
        while (curr.next != head) {
            if (curr.val <= insertVal && insertVal <= curr.next.val) break;
            if (curr.val > curr.next.val && (insertVal >= curr.val || insertVal <= curr.next.val)) break;
            curr = curr.next;
        }
        Node next = curr.next;
        curr.next = newNode;
        newNode.prev = curr;
        newNode.next = next;
        next.prev = newNode;
        return head;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(1)$.

---

## 6. Reverse a Doubly Linked List
**Đề bài chi tiết**: Đảo ngược một DLL.
**Phân tích thuật toán**: Đổi chỗ tham chiếu `next` và `prev` của tất cả các Node. Cập nhật Head ở cuối cùng.
**Mã nguồn Java**:
```java
class Solution {
    public Node reverseDLL(Node head) {
        Node curr = head;
        Node temp = null;
        while (curr != null) {
            temp = curr.prev;
            curr.prev = curr.next;
            curr.next = temp;
            curr = curr.prev;
        }
        if (temp != null) {
            head = temp.prev;
        }
        return head;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(1)$.

---

## 7. Browser History (Using DLL)
**Đề bài chi tiết**: Implement hệ thống lịch sử trình duyệt hỗ trợ `visit(url)`, `back(steps)`, `forward(steps)`.
**Phân tích thuật toán**: Sử dụng DLL để lưu lịch sử truy cập. Di chuyển con trỏ hiện tại lên xuống dựa theo số bước.
**Mã nguồn Java**:
```java
class BrowserHistory {
    class Node {
        String url;
        Node prev, next;
        Node(String u) { url = u; }
    }
    
    Node curr;

    public BrowserHistory(String homepage) {
        curr = new Node(homepage);
    }
    
    public void visit(String url) {
        Node newNode = new Node(url);
        curr.next = newNode;
        newNode.prev = curr;
        curr = newNode;
    }
    
    public String back(int steps) {
        while (curr.prev != null && steps > 0) {
            curr = curr.prev;
            steps--;
        }
        return curr.url;
    }
    
    public String forward(int steps) {
        while (curr.next != null && steps > 0) {
            curr = curr.next;
            steps--;
        }
        return curr.url;
    }
}
```
**Độ phức tạp**: Thêm $O(1)$, Back/Forward $O(S)$ với $S$ là số bước. Không gian $O(N)$.

---

## 8. Delete Node in a Doubly Linked List
**Đề bài chi tiết**: Cho head của DLL và một index `x` hoặc giá trị `x`, xóa Node đó.
**Phân tích thuật toán**: Tìm Node tương ứng và nối `prev` với `next` bỏ qua nó.
**Mã nguồn Java**:
```java
class Solution {
    Node deleteNode(Node head, int x) {
        if (head == null) return null;
        Node curr = head;
        for (int i = 1; i < x && curr != null; i++) {
            curr = curr.next;
        }
        if (curr == null) return head;
        
        if (curr.prev != null) curr.prev.next = curr.next;
        else head = curr.next;
        
        if (curr.next != null) curr.next.prev = curr.prev;
        
        return head;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(1)$.

---

## 9. Find pairs with given sum in DLL
**Đề bài chi tiết**: Tìm tất cả các cặp trong Sorted DLL có tổng bằng giá trị X.
**Phân tích thuật toán**: Sử dụng kỹ thuật Two Pointers, một trỏ Head đi tới, một trỏ Tail đi lui.
**Mã nguồn Java**:
```java
import java.util.*;

class Solution {
    public static ArrayList<ArrayList<Integer>> findPairsWithGivenSum(int target, Node head) {
        ArrayList<ArrayList<Integer>> res = new ArrayList<>();
        Node left = head;
        Node right = head;
        while (right.next != null) right = right.next;
        
        while (left != null && right != null && left != right && right.next != left) {
            int sum = left.val + right.val;
            if (sum == target) {
                res.add(new ArrayList<>(Arrays.asList(left.val, right.val)));
                left = left.next;
                right = right.prev;
            } else if (sum < target) {
                left = left.next;
            } else {
                right = right.prev;
            }
        }
        return res;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(1)$.

---

## 10. Remove duplicates from an unsorted doubly linked list
**Đề bài chi tiết**: Xóa các Node trùng lặp giữ nguyên thứ tự trong Unsorted DLL.
**Phân tích thuật toán**: Sử dụng HashSet để lưu lại các giá trị đã gặp. Xóa ngay khi trùng lặp.
**Mã nguồn Java**:
```java
import java.util.HashSet;

class Solution {
    public Node removeDuplicates(Node head) {
        if (head == null) return null;
        HashSet<Integer> seen = new HashSet<>();
        Node curr = head;
        while (curr != null) {
            if (seen.contains(curr.val)) {
                Node prev = curr.prev;
                Node next = curr.next;
                if (prev != null) prev.next = next;
                if (next != null) next.prev = prev;
            } else {
                seen.add(curr.val);
            }
            curr = curr.next;
        }
        return head;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(N)$.

---

## 11. Convert a given Binary Tree to Doubly Linked List
**Đề bài chi tiết**: Chuyển đổi một cây nhị phân (Binary Tree) thành một danh sách liên kết đôi (Doubly Linked List - DLL) tại chỗ (in-place). Các nút trong DLL phải được sắp xếp theo thứ tự duyệt giữa (in-order traversal) của cây nhị phân. Con trỏ `left` của cây đóng vai trò là `prev` trong DLL, và con trỏ `right` đóng vai trò là `next`.
**Phân tích thuật toán**: Sử dụng duyệt cây theo thứ tự giữa (in-order traversal). Dùng một con trỏ `prev` để lưu vết nút vừa được xử lý trước đó. Khi đang ở một nút `root`, ta cập nhật `root.left = prev` và `prev.right = root`. Cần lưu lại nút đầu tiên được xử lý (nút trái nhất) để làm phần tử `head` của DLL.
**Mã nguồn Java**:
```java
class Node {
    int val;
    Node left, right;
    Node(int val) { this.val = val; }
}

class Solution {
    Node prev = null;
    Node head = null;

    public Node bToDLL(Node root) {
        if (root == null) return null;

        bToDLL(root.left);

        if (prev == null) {
            head = root;
        } else {
            root.left = prev;
            prev.right = root;
        }
        prev = root;

        bToDLL(root.right);

        return head;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$ vì mỗi nút được duyệt một lần. Không gian $O(H)$ với $H$ là chiều cao của cây (chi phí cho call stack).

---

## 12. Sort a "k" sorted Doubly Linked List
**Đề bài chi tiết**: Cho một danh sách liên kết đôi mà mỗi nút cách vị trí đã sắp xếp đúng của nó nhiều nhất $k$ vị trí. Hãy sắp xếp lại danh sách này.
**Phân tích thuật toán**: Vì mỗi phần tử chỉ lệch tối đa $k$ vị trí, ta có thể sử dụng cấu trúc dữ liệu hàng đợi ưu tiên (Min Heap). Duyệt qua danh sách, đẩy $k+1$ phần tử đầu tiên vào Min Heap. Sau đó liên tục lấy phần tử nhỏ nhất ra từ Min Heap để đưa vào danh sách kết quả, đồng thời đẩy phần tử tiếp theo trong danh sách gốc vào Min Heap. 
**Mã nguồn Java**:
```java
import java.util.PriorityQueue;

class Solution {
    public Node sortAKSortedDLL(Node head, int k) {
        if (head == null || head.next == null) return head;

        PriorityQueue<Node> pq = new PriorityQueue<>((a, b) -> a.val - b.val);
        Node newHead = null, last = null;
        Node curr = head;

        for (int i = 0; curr != null && i <= k; i++) {
            pq.add(curr);
            curr = curr.next;
        }

        while (!pq.isEmpty()) {
            Node minNode = pq.poll();
            if (newHead == null) {
                newHead = minNode;
                newHead.prev = null;
                last = newHead;
            } else {
                last.next = minNode;
                minNode.prev = last;
                last = minNode;
            }

            if (curr != null) {
                pq.add(curr);
                curr = curr.next;
            }
        }
        last.next = null;
        return newHead;
    }
}
```
**Độ phức tạp**: Thời gian $O(N \log K)$ với $N$ là số lượng phần tử của DLL. Không gian $O(K)$ cho kích thước của Min Heap.

---

## 13. Rotate DoublyLinked list by N nodes
**Đề bài chi tiết**: Xoay danh sách liên kết đôi sang trái $N$ bước. Đảm bảo danh sách vẫn giữ cấu trúc hợp lệ (các con trỏ `prev` và `next` chính xác).
**Phân tích thuật toán**: Đầu tiên duyệt để tìm kích thước danh sách và phần tử `tail` (phần tử cuối). Nối `tail.next = head` và `head.prev = tail` để tạo thành danh sách vòng (circular). Sau đó, di chuyển con trỏ từ `head` đi $N \pmod{\text{size}}$ bước để tìm phần tử `head` mới. Cuối cùng, ngắt liên kết giữa `head` mới và phần tử ngay trước nó để tạo lại danh sách thẳng.
**Mã nguồn Java**:
```java
class Solution {
    public Node rotateDLL(Node head, int n) {
        if (head == null || head.next == null || n == 0) return head;

        Node tail = head;
        int size = 1;
        while (tail.next != null) {
            tail = tail.next;
            size++;
        }

        n = n % size;
        if (n == 0) return head;

        tail.next = head;
        head.prev = tail;

        Node curr = head;
        for (int i = 0; i < n; i++) {
            curr = curr.next;
        }

        Node newHead = curr;
        Node newTail = curr.prev;

        newTail.next = null;
        newHead.prev = null;

        return newHead;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$ với $N$ là số lượng phần tử. Không gian $O(1)$.

---

## 14. Clone a Doubly Linked List with next and random pointer
**Đề bài chi tiết**: Cung cấp một danh sách liên kết, trong đó mỗi nút chứa một con trỏ `random` có thể trỏ đến bất kỳ nút nào trong danh sách hoặc `null`. Hãy tạo một bản sao sâu (deep copy) của danh sách này.
**Phân tích thuật toán**: Để đạt không gian $O(1)$, ta chèn các nút bản sao ngay sau các nút gốc tương ứng ($A \to A' \to B \to B'$). Sau đó, cập nhật con trỏ `random` cho các nút bản sao ($A'.random = A.random.next$). Cuối cùng, tách danh sách xen kẽ này thành hai danh sách riêng biệt.
**Mã nguồn Java**:
```java
class Node {
    int val;
    Node next, prev, random;
    Node(int val) { this.val = val; }
}

class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) return null;

        Node curr = head;
        while (curr != null) {
            Node clone = new Node(curr.val);
            clone.next = curr.next;
            clone.prev = curr;
            if (curr.next != null) {
                curr.next.prev = clone;
            }
            curr.next = clone;
            curr = clone.next;
        }

        curr = head;
        while (curr != null) {
            if (curr.random != null) {
                curr.next.random = curr.random.next;
            }
            curr = curr.next.next;
        }

        curr = head;
        Node copyHead = head.next;
        while (curr != null) {
            Node clone = curr.next;
            curr.next = clone.next;
            if (curr.next != null) {
                curr.next.prev = curr;
            }
            if (clone.next != null) {
                clone.next = clone.next.next;
            }
            curr = curr.next;
        }
        return copyHead;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$. Không gian $O(1)$ (không dùng HashMap).

---

## 15. Find the middle element of Doubly Linked List
**Đề bài chi tiết**: Tìm phần tử nằm ở vị trí chính giữa của danh sách liên kết đôi. Nếu danh sách có số lượng phần tử chẵn, trả về phần tử ở vị trí thứ $(N/2) + 1$.
**Phân tích thuật toán**: Có thể dùng thuật toán Rùa và Thỏ (Fast and Slow pointers). Con trỏ nhanh đi 2 bước, con trỏ chậm đi 1 bước. Khi con trỏ nhanh chạy đến cuối danh sách thì con trỏ chậm ở chính giữa.
**Mã nguồn Java**:
```java
class Solution {
    public Node findMiddle(Node head) {
        if (head == null) return null;
        
        Node slow = head;
        Node fast = head;
        
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        return slow;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$ (chính xác là $N/2$ vòng lặp). Không gian $O(1)$.

---

## 16. Check if Doubly Linked List is a Palindrome
**Đề bài chi tiết**: Kiểm tra xem một danh sách liên kết đôi có phải là chuỗi đối xứng (Palindrome) hay không.
**Phân tích thuật toán**: Ở DLL ta có thể dễ dàng tìm phần tử `tail`. Sau khi có `tail`, sử dụng hai con trỏ: một trỏ tới `head` (đi tới), một trỏ tới `tail` (đi lui). So sánh giá trị ở mỗi bước cho đến khi hai con trỏ gặp nhau hoặc vượt qua nhau.
**Mã nguồn Java**:
```java
class Solution {
    public boolean isPalindrome(Node head) {
        if (head == null) return true;

        Node tail = head;
        while (tail.next != null) {
            tail = tail.next;
        }

        Node left = head;
        Node right = tail;

        while (left != right && left.prev != right) {
            if (left.val != right.val) {
                return false;
            }
            left = left.next;
            right = right.prev;
        }

        return true;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$ để tìm tail và $O(N)$ để so sánh, tổng là $O(N)$. Không gian $O(1)$.

---

## 17. Merge two sorted Doubly Linked Lists
**Đề bài chi tiết**: Cho hai danh sách liên kết đôi đã được sắp xếp tăng dần. Hãy kết hợp chúng lại thành một danh sách liên kết đôi duy nhất cũng được sắp xếp tăng dần.
**Phân tích thuật toán**: Sử dụng kỹ thuật hai con trỏ (Two Pointers) duyệt qua 2 danh sách. Dùng một nút giả (dummy node) để dễ dàng thao tác chèn. So sánh giá trị của hai nút, nút nhỏ hơn sẽ được đưa vào danh sách kết quả, đồng thời cập nhật cả con trỏ `next` và `prev`.
**Mã nguồn Java**:
```java
class Solution {
    public Node merge(Node head1, Node head2) {
        Node dummy = new Node(0);
        Node tail = dummy;

        while (head1 != null && head2 != null) {
            if (head1.val <= head2.val) {
                tail.next = head1;
                head1.prev = tail;
                head1 = head1.next;
            } else {
                tail.next = head2;
                head2.prev = tail;
                head2 = head2.next;
            }
            tail = tail.next;
        }

        if (head1 != null) {
            tail.next = head1;
            head1.prev = tail;
        }
        if (head2 != null) {
            tail.next = head2;
            head2.prev = tail;
        }

        Node res = dummy.next;
        if (res != null) res.prev = null;
        return res;
    }
}
```
**Độ phức tạp**: Thời gian $O(N + M)$ với $N$ và $M$ là độ dài hai danh sách. Không gian $O(1)$.

---

## 18. Swap Kth node from beginning with Kth node from end
**Đề bài chi tiết**: Cho một danh sách liên kết đôi và một số nguyên $K$. Hãy hoán đổi vị trí của nút thứ $K$ tính từ đầu và nút thứ $K$ tính từ cuối danh sách.
**Phân tích thuật toán**: Đầu tiên, đếm tổng số phần tử $N$ của danh sách. Nếu $2K - 1 == N$, 2 nút trùng nhau nên không cần hoán đổi. Tìm nút thứ $K$ từ đầu ($X$) và nút thứ $K$ từ cuối ($Y$). Cẩn thận xử lý con trỏ `prev` và `next` của các nút liên quan. Nếu $X$ hoặc $Y$ là `head` hoặc `tail`, cần cập nhật con trỏ `head` và `tail`.
**Mã nguồn Java**:
```java
class Solution {
    public Node swapKthNode(Node head, int k) {
        int n = 0;
        Node curr = head;
        while (curr != null) {
            n++;
            curr = curr.next;
        }

        if (n < k || 2 * k - 1 == n) return head;

        Node x = head, y = head;
        for (int i = 1; i < k; i++) x = x.next;
        for (int i = 1; i < n - k + 1; i++) y = y.next;

        if (x.prev != null) x.prev.next = y;
        if (y.prev != null) y.prev.next = x;

        Node temp = x.prev;
        x.prev = y.prev;
        y.prev = temp;

        if (x.next != null) x.next.prev = y;
        if (y.next != null) y.next.prev = x;

        temp = x.next;
        x.next = y.next;
        y.next = temp;

        if (k == 1) head = y;
        if (k == n) head = x;

        return head;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$ để duyệt. Không gian $O(1)$.

---

## 19. Reverse a Doubly Linked List in groups of given size
**Đề bài chi tiết**: Cho một danh sách liên kết đôi và một số $K$. Đảo ngược từng nhóm $K$ nút trong danh sách. Nếu ở nhóm cuối cùng không đủ $K$ nút, vẫn phải đảo ngược chúng.
**Phân tích thuật toán**: Sử dụng đệ quy. Trong mỗi lần gọi, đảo ngược tối đa $K$ nút. Con trỏ `next` của nút đầu tiên (sau khi đảo trở thành nút cuối nhóm) sẽ nối với kết quả của lời gọi đệ quy tiếp theo. Cập nhật chính xác `prev` cho các nút đã bị thay đổi liên kết.
**Mã nguồn Java**:
```java
class Solution {
    public Node reverseByK(Node head, int k) {
        if (head == null) return null;

        Node curr = head;
        Node temp = null;
        Node newHead = null;
        int count = 0;

        while (curr != null && count < k) {
            temp = curr.prev;
            curr.prev = curr.next;
            curr.next = temp;
            newHead = curr;
            curr = curr.prev;
            count++;
        }

        if (count >= k) {
            Node rest = reverseByK(curr, k);
            head.next = rest;
            if (rest != null) {
                rest.prev = head;
            }
        } else {
            head.next = null;
        }

        return newHead;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$ do duyệt qua mỗi phần tử một lần. Không gian $O(N/K)$ cho ngăn xếp đệ quy.

---

## 20. Delete all occurrences of a given key in a doubly linked list
**Đề bài chi tiết**: Cho một danh sách liên kết đôi và một số nguyên `x`. Hãy xóa tất cả các nút có giá trị bằng `x` ra khỏi danh sách.
**Phân tích thuật toán**: Duyệt danh sách từ đầu đến cuối. Nếu gặp nút có `val == x`, ta gỡ nút này ra khỏi danh sách bằng cách nối `prev` của nó với `next` của nó. Đặc biệt lưu ý nếu nút bị xóa là `head` thì phải cập nhật lại `head`.
**Mã nguồn Java**:
```java
class Solution {
    public Node deleteAllOccurrences(Node head, int x) {
        Node curr = head;
        while (curr != null) {
            if (curr.val == x) {
                if (curr == head) {
                    head = curr.next;
                }
                
                Node nextNode = curr.next;
                Node prevNode = curr.prev;
                
                if (nextNode != null) {
                    nextNode.prev = prevNode;
                }
                if (prevNode != null) {
                    prevNode.next = nextNode;
                }
                
                curr = nextNode;
            } else {
                curr = curr.next;
            }
        }
        return head;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$ vì duyệt danh sách một lần. Không gian $O(1)$.

---

## 21. Insert Node in sorted Doubly Linked List
**Đề bài chi tiết**: Cho một danh sách liên kết đôi đã được sắp xếp tăng dần và một số nguyên `x`. Hãy chèn một nút mới chứa giá trị `x` vào đúng vị trí để danh sách vẫn giữ được thứ tự sắp xếp.
**Phân tích thuật toán**: Duyệt danh sách từ đầu đến cuối để tìm vị trí thích hợp. Nút mới sẽ được chèn trước nút đầu tiên có giá trị lớn hơn hoặc bằng `x`. Cần lưu ý 3 trường hợp đặc biệt: chèn vào đầu danh sách (khi `x` nhỏ hơn tất cả), chèn vào giữa danh sách, và chèn vào cuối danh sách.
**Mã nguồn Java**:
```java
class Solution {
    public Node sortedInsert(Node head, int x) {
        Node newNode = new Node(x);
        
        if (head == null) {
            return newNode;
        }
        
        if (head.val >= x) {
            newNode.next = head;
            head.prev = newNode;
            return newNode;
        }
        
        Node curr = head;
        while (curr.next != null && curr.next.val < x) {
            curr = curr.next;
        }
        
        Node nextNode = curr.next;
        curr.next = newNode;
        newNode.prev = curr;
        newNode.next = nextNode;
        
        if (nextNode != null) {
            nextNode.prev = newNode;
        }
        
        return head;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$ vì phải duyệt danh sách để tìm vị trí chèn. Không gian $O(1)$.

---

## 22. Delete duplicates from sorted DLL
**Đề bài chi tiết**: Cho một danh sách liên kết đôi đã được sắp xếp tăng dần. Hãy xóa tất cả các nút trùng lặp sao cho mỗi phần tử chỉ xuất hiện duy nhất một lần trong danh sách.
**Phân tích thuật toán**: Vì danh sách đã được sắp xếp, các phần tử trùng lặp sẽ nằm liền kề nhau. Ta chỉ cần dùng một con trỏ duyệt qua danh sách, nếu giá trị của nút hiện tại bằng với giá trị của nút tiếp theo, ta tiến hành xóa nút tiếp theo bằng cách cập nhật con trỏ `next` của nút hiện tại và `prev` của nút sau nút tiếp theo.
**Mã nguồn Java**:
```java
class Solution {
    Node removeDuplicates(Node head){
        if (head == null || head.next == null) return head;
        
        Node curr = head;
        while (curr != null && curr.next != null) {
            if (curr.val == curr.next.val) {
                Node nextNextNode = curr.next.next;
                curr.next = nextNextNode;
                if (nextNextNode != null) {
                    nextNextNode.prev = curr;
                }
            } else {
                curr = curr.next;
            }
        }
        
        return head;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$ duyệt qua mỗi nút một lần. Không gian $O(1)$.

---

## 23. Extract leaves of a Binary Tree in a Doubly Linked List
**Đề bài chi tiết**: Cho một cây nhị phân (Binary Tree). Hãy trích xuất tất cả các nút lá (leaf nodes) từ trái sang phải để tạo thành một danh sách liên kết đôi. Đồng thời, loại bỏ các nút lá này ra khỏi cây nhị phân ban đầu.
**Phân tích thuật toán**: Sử dụng thuật toán DFS (duyệt theo thứ tự in-order hoặc post-order từ trái sang phải). Khi gặp một nút lá, tách nó khỏi cây (trả về `null` cho nút cha) và thêm nó vào DLL. Cần dùng một con trỏ toàn cục để theo dõi phần tử cuối cùng (`tail`) của DLL nhằm chèn phần tử mới vào cuối với thao tác $O(1)$.
**Mã nguồn Java**:
```java
class Node {
    int val;
    Node left, right;
    Node(int val) { this.val = val; }
}

class Solution {
    Node head = null;
    Node tail = null;

    public Node extractLeaves(Node root) {
        if (root == null) return null;
        
        if (root.left == null && root.right == null) {
            if (head == null) {
                head = root;
                tail = root;
            } else {
                tail.right = root;
                root.left = tail;
                tail = root;
            }
            return null; // Xóa lá khỏi cây
        }
        
        root.left = extractLeaves(root.left);
        root.right = extractLeaves(root.right);
        
        return root;
    }
    
    // Gọi hàm này để lấy head của DLL
    public Node getLeavesDLL(Node root) {
        extractLeaves(root);
        return head;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$ duyệt tất cả các nút. Không gian $O(H)$ với $H$ là chiều cao của cây do call stack.

---

## 24. Find Size of Doubly Linked List
**Đề bài chi tiết**: Cho phần tử đầu (`head`) của một danh sách liên kết đôi, đếm và trả về tổng số lượng các nút trong danh sách.
**Phân tích thuật toán**: Khởi tạo biến đếm bằng 0. Sử dụng một con trỏ bắt đầu từ `head`, duyệt qua danh sách bằng cách đi theo con trỏ `next`. Mỗi khi duyệt qua một nút, tăng biến đếm lên 1 cho đến khi gặp `null`.
**Mã nguồn Java**:
```java
class Solution {
    public int getLength(Node head) {
        int count = 0;
        Node curr = head;
        
        while (curr != null) {
            count++;
            curr = curr.next;
        }
        
        return count;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$ vì phải lướt qua toàn bộ danh sách. Không gian $O(1)$.

---

## 25. Alternating split of a given Doubly Linked List
**Đề bài chi tiết**: Cho một danh sách liên kết đôi. Hãy chia xen kẽ (alternating split) danh sách này thành 2 danh sách con: một danh sách chứa các nút ở vị trí lẻ, danh sách còn lại chứa các nút ở vị trí chẵn. (Ví dụ: $1 \to 2 \to 3 \to 4 \to 5$ thành $1 \to 3 \to 5$ và $2 \to 4$).
**Phân tích thuật toán**: Dùng hai con trỏ `curr1` và `curr2` để theo dõi phần tử hiện tại của 2 danh sách con mới. Ta có thể tái sử dụng trực tiếp các con trỏ `next` và `prev` của danh sách cũ bằng cách duyệt nhảy cóc (`curr.next = curr.next.next`).
**Mã nguồn Java**:
```java
class Solution {
    public Node[] alternatingSplit(Node head) {
        if (head == null) return new Node[]{null, null};
        if (head.next == null) return new Node[]{head, null};
        
        Node head1 = head;
        Node head2 = head.next;
        
        Node curr1 = head1;
        Node curr2 = head2;
        
        while (curr1 != null && curr2 != null) {
            curr1.next = curr2.next;
            if (curr1.next != null) {
                curr1.next.prev = curr1;
            }
            curr1 = curr1.next;
            
            if (curr1 != null) {
                curr2.next = curr1.next;
                if (curr2.next != null) {
                    curr2.next.prev = curr2;
                }
                curr2 = curr2.next;
            }
        }
        
        return new Node[]{head1, head2};
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$. Không gian $O(1)$ vì tái sử dụng cấu trúc sẵn có.

---

## 26. Convert Singly Linked List to Doubly Linked List
**Đề bài chi tiết**: Cho một danh sách liên kết đơn (Singly Linked List) chỉ có con trỏ `next`. Hãy chuyển đổi nó thành danh sách liên kết đôi (Doubly Linked List) bằng cách thiết lập thêm con trỏ `prev` cho mỗi nút.
**Phân tích thuật toán**: Duyệt qua danh sách bằng một con trỏ `curr`. Cùng lúc đó duy trì một con trỏ `prevNode` lưu nút liền trước của `curr`. Ở mỗi bước, gán `curr.prev = prevNode` và cập nhật `prevNode = curr`, sau đó nhích `curr` sang `next`. Nút `head` sẽ có `prev = null`.
**Mã nguồn Java**:
```java
class Node {
    int val;
    Node next, prev;
    Node(int val) { this.val = val; }
}

class Solution {
    public Node convertToDLL(Node head) {
        if (head == null) return null;
        
        Node curr = head;
        Node prevNode = null;
        
        while (curr != null) {
            curr.prev = prevNode;
            prevNode = curr;
            curr = curr.next;
        }
        
        return head;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$ đi qua từng phần tử. Không gian $O(1)$.

---

## 27. Move all vowels to the end of a DLL
**Đề bài chi tiết**: Cung cấp một danh sách liên kết đôi chứa các ký tự (characters). Hãy di chuyển tất cả các nút chứa nguyên âm ('a', 'e', 'i', 'o', 'u' - không phân biệt hoa thường) xuống phía cuối của danh sách. Đảm bảo thứ tự tương đối của các phụ âm và nguyên âm được giữ nguyên.
**Phân tích thuật toán**: Trước hết, tìm phần tử cuối cùng `tail`. Dùng một vòng lặp từ `head` đến `tail` (lưu ý chỉ xét các phần tử ban đầu). Nếu nút hiện tại là nguyên âm, tách nó ra khỏi vị trí hiện hành và chèn nó vào sau đuôi danh sách thông qua con trỏ `tail`, sau đó cập nhật `tail` mới.
**Mã nguồn Java**:
```java
class Node {
    char val;
    Node prev, next;
    Node(char val) { this.val = val; }
}

class Solution {
    public boolean isVowel(char c) {
        c = Character.toLowerCase(c);
        return c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u';
    }

    public Node arrangeVowels(Node head) {
        if (head == null || head.next == null) return head;

        Node tail = head;
        while (tail.next != null) {
            tail = tail.next;
        }
        
        Node origTail = tail;
        Node curr = head;
        
        while (curr != null && curr != origTail) {
            Node nextNode = curr.next;
            if (isVowel(curr.val)) {
                if (curr == head) {
                    head = head.next;
                    head.prev = null;
                } else {
                    curr.prev.next = curr.next;
                    curr.next.prev = curr.prev;
                }
                
                tail.next = curr;
                curr.prev = tail;
                curr.next = null;
                tail = curr;
            }
            curr = nextNode;
        }
        
        if (isVowel(origTail.val) && origTail != tail) {
            if (origTail == head) {
                head = head.next;
                head.prev = null;
            } else {
                origTail.prev.next = origTail.next;
                if (origTail.next != null) origTail.next.prev = origTail.prev;
            }
            tail.next = origTail;
            origTail.prev = tail;
            origTail.next = null;
            tail = origTail;
        }

        return head;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$. Không gian $O(1)$.

---

## 28. Add 1 to a number represented as DLL
**Đề bài chi tiết**: Cho một số nguyên lớn được biểu diễn dưới dạng danh sách liên kết đôi (mỗi nút chứa 1 chữ số). Hãy cộng 1 vào số này và trả về danh sách biểu diễn kết quả. Nếu có số dư, danh sách có thể dài thêm.
**Phân tích thuật toán**: Do tính chất của phép cộng số diễn ra từ hàng đơn vị trở lên (từ phải sang trái), ta duyệt đến nút cuối (tail) rồi lùi về đầu (head). Cộng thêm phần dư (carry) vào mỗi chữ số, ở nút tail carry = 1. Nếu đến `head` mà vẫn còn dư, ta chèn thêm một nút mang giá trị 1 vào đầu danh sách.
**Mã nguồn Java**:
```java
class Solution {
    public Node addOne(Node head) {
        if (head == null) return null;
        
        Node tail = head;
        while (tail.next != null) {
            tail = tail.next;
        }
        
        Node curr = tail;
        int carry = 1;
        
        while (curr != null) {
            int sum = curr.val + carry;
            curr.val = sum % 10;
            carry = sum / 10;
            
            if (carry == 0) break;
            curr = curr.prev;
        }
        
        if (carry > 0) {
            Node newHead = new Node(carry);
            newHead.next = head;
            head.prev = newHead;
            head = newHead;
        }
        
        return head;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$ với $N$ là số chữ số. Không gian $O(1)$.

---

## 29. Subtract two numbers represented as DLL
**Đề bài chi tiết**: Cho hai danh sách liên kết đôi biểu diễn 2 số nguyên dương. Hãy thực hiện phép trừ số lớn cho số nhỏ và trả về danh sách kết quả. Bỏ qua các số 0 ở đầu kết quả.
**Phân tích thuật toán**: Đầu tiên, so sánh chiều dài và giá trị từ trái qua phải để xác định số nào lớn hơn (để đảm bảo phép tính luôn có kết quả dương). Tới các nút `tail` của cả hai, rồi đi ngược lên (bằng `prev`) để trừ từng chữ số, kèm theo số nhớ (borrow) khi chữ số bị trừ nhỏ hơn. Sau cùng loại bỏ các số 0 vô nghĩa ở đầu.
**Mã nguồn Java**:
```java
class Solution {
    public Node subtractDLL(Node head1, Node head2) {
        int len1 = getLength(head1);
        int len2 = getLength(head2);
        
        if (len1 < len2 || (len1 == len2 && !isFirstLarger(head1, head2))) {
            Node temp = head1; head1 = head2; head2 = temp;
        }
        
        Node tail1 = getTail(head1), tail2 = getTail(head2);
        Node dummy = new Node(0);
        Node resTail = dummy;
        int borrow = 0;
        
        while (tail1 != null) {
            int v1 = tail1.val - borrow;
            int v2 = (tail2 != null) ? tail2.val : 0;
            
            if (v1 < v2) {
                v1 += 10;
                borrow = 1;
            } else {
                borrow = 0;
            }
            
            Node newNode = new Node(v1 - v2);
            newNode.next = resTail;
            resTail.prev = newNode;
            resTail = newNode;
            
            tail1 = tail1.prev;
            if (tail2 != null) tail2 = tail2.prev;
        }
        
        Node resHead = resTail;
        resHead.next.prev = null; 
        
        while (resHead != null && resHead.val == 0 && resHead.next != dummy) {
            resHead = resHead.next;
            resHead.prev = null;
        }
        
        if (resHead == dummy) return new Node(0);
        
        Node last = resHead;
        while (last.next != dummy) last = last.next;
        last.next = null;
        
        return resHead;
    }
    
    private int getLength(Node h) {
        int len = 0; while (h != null) { len++; h = h.next; } return len;
    }
    private Node getTail(Node h) {
        while (h != null && h.next != null) h = h.next; return h;
    }
    private boolean isFirstLarger(Node h1, Node h2) {
        while (h1 != null && h2 != null) {
            if (h1.val > h2.val) return true;
            if (h1.val < h2.val) return false;
            h1 = h1.next; h2 = h2.next;
        }
        return true;
    }
}
```
**Độ phức tạp**: Thời gian $O(N + M)$. Không gian $O(\max(N, M))$ cho danh sách mới.

---

## 30. Sort a bitonic doubly linked list
**Đề bài chi tiết**: Cho một danh sách liên kết đôi dạng Bitonic (tức là tăng dần trước rồi sau đó giảm dần). Hãy sắp xếp danh sách này thành một danh sách tăng dần thuần túy.
**Phân tích thuật toán**: Vì danh sách có cấu trúc Bitonic, phần đầu đã được sắp xếp tăng dần, phần cuối giảm dần. Ta đi tìm nút mà tại đó giá trị bắt đầu giảm dần. Tại điểm gãy này, ta ngắt thành hai danh sách. Danh sách thứ hai bị đảo ngược (reverse). Sau khi đảo ngược, ta có hai danh sách tăng dần, áp dụng kỹ thuật "Merge Two Sorted Lists" để ghép chúng lại.
**Mã nguồn Java**:
```java
class Solution {
    public Node sortBitonicDLL(Node head) {
        if (head == null || head.next == null) return head;
        
        Node curr = head;
        while (curr.next != null && curr.val <= curr.next.val) {
            curr = curr.next;
        }
        
        if (curr.next == null) return head;
        
        Node head2 = curr.next;
        curr.next = null;
        head2.prev = null;
        
        head2 = reverseDLL(head2);
        
        return merge(head, head2);
    }
    
    private Node reverseDLL(Node head) {
        Node curr = head, temp = null;
        while (curr != null) {
            temp = curr.prev;
            curr.prev = curr.next;
            curr.next = temp;
            curr = curr.prev;
        }
        if (temp != null) head = temp.prev;
        return head;
    }
    
    private Node merge(Node head1, Node head2) {
        Node dummy = new Node(0);
        Node tail = dummy;
        
        while (head1 != null && head2 != null) {
            if (head1.val <= head2.val) {
                tail.next = head1; head1.prev = tail;
                head1 = head1.next;
            } else {
                tail.next = head2; head2.prev = tail;
                head2 = head2.next;
            }
            tail = tail.next;
        }
        
        if (head1 != null) { tail.next = head1; head1.prev = tail; }
        if (head2 != null) { tail.next = head2; head2.prev = tail; }
        
        Node res = dummy.next;
        if (res != null) res.prev = null;
        return res;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$ (duyệt, đảo ngược, merge). Không gian $O(1)$.
