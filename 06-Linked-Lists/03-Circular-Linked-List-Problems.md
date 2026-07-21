# Circular Linked List - Problems

Tài liệu này bao gồm 30 bài tập về danh sách liên kết vòng (Circular Linked List). 10 bài đầu tiên có đầy đủ đề bài, phân tích, mã nguồn Java và độ phức tạp. 20 bài tiếp theo được tóm tắt ý tưởng.

---

## 1. Design Circular Queue (using Linked List)

**Đề bài chi tiết**:
Thiết kế một hàng đợi vòng (Circular Queue) sử dụng Circular Linked List. Hàng đợi có giới hạn kích thước (capacity) hoặc không giới hạn. Cần hỗ trợ các thao tác: `enQueue` (thêm vào cuối), `deQueue` (xóa ở đầu), `Front` (lấy phần tử đầu), `Rear` (lấy phần tử cuối), `isEmpty`, `isFull`.

**Phân tích thuật toán**:
Sử dụng một Singly Circular Linked List với con trỏ `tail` và biến đếm `size`.
- `enQueue`: Thêm Node vào sau `tail` và cập nhật `tail` mới.
- `deQueue`: Xóa Node sau `tail` (tức là `head`). Nếu chỉ còn 1 Node, set `tail = null`.

**Mã nguồn Java**:
```java
class MyCircularQueue {
    class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }
    
    private Node tail;
    private int size;
    private int capacity;

    public MyCircularQueue(int k) {
        this.capacity = k;
        this.size = 0;
        this.tail = null;
    }
    
    public boolean enQueue(int value) {
        if (isFull()) return false;
        Node newNode = new Node(value);
        if (isEmpty()) {
            tail = newNode;
            tail.next = tail;
        } else {
            newNode.next = tail.next;
            tail.next = newNode;
            tail = newNode;
        }
        size++;
        return true;
    }
    
    public boolean deQueue() {
        if (isEmpty()) return false;
        if (size == 1) {
            tail = null;
        } else {
            tail.next = tail.next.next;
        }
        size--;
        return true;
    }
    
    public int Front() {
        if (isEmpty()) return -1;
        return tail.next.val;
    }
    
    public int Rear() {
        if (isEmpty()) return -1;
        return tail.val;
    }
    
    public boolean isEmpty() { return size == 0; }
    
    public boolean isFull() { return size == capacity; }
}
```

**Độ phức tạp**:
- Thời gian: $O(1)$ cho tất cả các thao tác.
- Không gian: $O(K)$ với $K$ là sức chứa (capacity).

---

## 2. Design Circular Deque

**Đề bài chi tiết**:
Thiết kế một hàng đợi hai đầu vòng (Circular Deque). Hỗ trợ các thao tác: `insertFront`, `insertLast`, `deleteFront`, `deleteLast`, `getFront`, `getRear`, `isEmpty`, `isFull`.

**Phân tích thuật toán**:
Sử dụng Doubly Circular Linked List để tối ưu `deleteLast` thành $O(1)$. Mỗi Node có `prev` và `next`. Khởi tạo một Node `dummy` (hoặc con trỏ `head` tùy cách cài) trỏ vòng lại để dễ xử lý. Trong mã dưới, dùng `head` và `tail`.

**Mã nguồn Java**:
```java
class MyCircularDeque {
    class DNode {
        int val;
        DNode prev, next;
        DNode(int val) { this.val = val; }
    }
    
    private DNode head, tail;
    private int size, capacity;

    public MyCircularDeque(int k) {
        this.capacity = k;
        this.size = 0;
    }
    
    public boolean insertFront(int value) {
        if (isFull()) return false;
        DNode node = new DNode(value);
        if (isEmpty()) {
            head = tail = node;
            head.next = head; head.prev = head;
        } else {
            node.next = head; node.prev = tail;
            head.prev = node; tail.next = node;
            head = node;
        }
        size++;
        return true;
    }
    
    public boolean insertLast(int value) {
        if (isFull()) return false;
        DNode node = new DNode(value);
        if (isEmpty()) {
            head = tail = node;
            head.next = head; head.prev = head;
        } else {
            node.prev = tail; node.next = head;
            tail.next = node; head.prev = node;
            tail = node;
        }
        size++;
        return true;
    }
    
    public boolean deleteFront() {
        if (isEmpty()) return false;
        if (size == 1) { head = tail = null; }
        else {
            head = head.next;
            head.prev = tail;
            tail.next = head;
        }
        size--;
        return true;
    }
    
    public boolean deleteLast() {
        if (isEmpty()) return false;
        if (size == 1) { head = tail = null; }
        else {
            tail = tail.prev;
            tail.next = head;
            head.prev = tail;
        }
        size--;
        return true;
    }
    
    public int getFront() { return isEmpty() ? -1 : head.val; }
    public int getRear() { return isEmpty() ? -1 : tail.val; }
    public boolean isEmpty() { return size == 0; }
    public boolean isFull() { return size == capacity; }
}
```

**Độ phức tạp**:
- Thời gian: $O(1)$ cho mọi thao tác.
- Không gian: $O(K)$.

---

## 3. Split a Circular Linked List into two halves

**Đề bài chi tiết**:
Cho một Singly Circular Linked List. Hãy chia danh sách này thành hai Circular Linked Lists riêng biệt. Nếu số lượng Node là lẻ, danh sách thứ nhất có nhiều hơn danh sách thứ hai 1 Node.

**Phân tích thuật toán**:
Sử dụng kỹ thuật Rùa và Thỏ (Slow & Fast pointers).
- Slow nhảy 1 bước, Fast nhảy 2 bước.
- Khi `fast.next == head` (độ dài lẻ) hoặc `fast.next.next == head` (độ dài chẵn), `slow` sẽ ở cuối danh sách thứ nhất. `fast` sẽ ở cuối danh sách thứ hai.
- Cắt và nối lại các `head` và `tail`.

**Mã nguồn Java**:
```java
class SplitCircularList {
    static class Node {
        int data;
        Node next;
        Node(int data) { this.data = data; }
    }

    public static Node[] splitList(Node head) {
        if (head == null) return new Node[]{null, null};
        if (head.next == head) return new Node[]{head, null};
        
        Node slow = head;
        Node fast = head;
        
        while (fast.next != head && fast.next.next != head) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        // Even nodes
        if (fast.next.next == head) {
            fast = fast.next;
        }
        
        Node head1 = head;
        Node head2 = slow.next;
        
        // Make second half circular
        fast.next = head2;
        // Make first half circular
        slow.next = head1;
        
        return new Node[]{head1, head2};
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$ với $N$ là số phần tử.
- Không gian: $O(1)$.

---

## 4. Insert in a Sorted Circular Linked List

**Đề bài chi tiết**:
Cho một con trỏ trỏ đến bất kỳ Node nào trong danh sách liên kết vòng đã được sắp xếp tăng dần. Hãy chèn một giá trị `insertVal` vào đúng vị trí để danh sách vẫn giữ được tính sắp xếp.

**Phân tích thuật toán**:
Dùng hai con trỏ `prev` và `curr` để duyệt.
Có 3 trường hợp chèn:
1. `prev.val <= insertVal <= curr.val`: Nằm ở giữa hai số bình thường.
2. `prev.val > curr.val` (đây là điểm kết nối cuối -> đầu): Nếu `insertVal >= prev.val` hoặc `insertVal <= curr.val` thì chèn vào đây.
3. Duyệt hết một vòng mà chưa chèn được (tất cả phần tử giống nhau): Chèn ở đâu cũng được.

**Mã nguồn Java**:
```java
class InsertSortedCircular {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public Node insert(Node head, int insertVal) {
        Node newNode = new Node(insertVal);
        if (head == null) {
            newNode.next = newNode;
            return newNode;
        }
        
        Node prev = head;
        Node curr = head.next;
        boolean inserted = false;
        
        do {
            if (prev.val <= insertVal && insertVal <= curr.val) {
                inserted = true;
                break;
            }
            if (prev.val > curr.val) {
                if (insertVal >= prev.val || insertVal <= curr.val) {
                    inserted = true;
                    break;
                }
            }
            prev = curr;
            curr = curr.next;
        } while (prev != head);
        
        prev.next = newNode;
        newNode.next = curr;
        return head;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$.
- Không gian: $O(1)$.

---

## 5. Josephus Problem (Find the Winner of the Circular Game)

**Đề bài chi tiết**:
Có $N$ người đứng thành một vòng tròn, đánh số từ 1 đến $N$. Bắt đầu từ người 1, đếm đến người thứ $k$ và loại người đó khỏi vòng. Quá trình tiếp tục lặp lại với người tiếp theo cho đến khi chỉ còn 1 người sống sót. Hãy tìm người đó. Thực hiện bằng cách mô phỏng Circular Linked List.

**Phân tích thuật toán**:
Tạo một Circular Linked List từ 1 đến $N$. Dùng vòng lặp, mỗi bước duyệt đi $(k-1)$ lần và loại bỏ Node tiếp theo. Lặp cho đến khi `node.next == node`.

**Mã nguồn Java**:
```java
class JosephusProblem {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public int findTheWinner(int n, int k) {
        Node head = new Node(1);
        Node prev = head;
        for (int i = 2; i <= n; i++) {
            Node curr = new Node(i);
            prev.next = curr;
            prev = curr;
        }
        prev.next = head; // Make it circular
        
        Node curr = head;
        Node tempPrev = prev; // Pointer to node before curr
        
        while (curr.next != curr) {
            // Move k-1 steps
            for (int i = 0; i < k - 1; i++) {
                tempPrev = curr;
                curr = curr.next;
            }
            // Remove curr
            tempPrev.next = curr.next;
            curr = tempPrev.next; // Start from next person
        }
        
        return curr.val;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N \times k)$ (cho cách mô phỏng này).
- Không gian: $O(N)$ (cấp phát $N$ Node). (Có thể dùng mảng/đệ quy đạt $O(N)$ thời gian, $O(1)$ không gian).

---

## 6. Check if Linked List is Circular

**Đề bài chi tiết**:
Cho `head` của một Singly Linked List, kiểm tra xem danh sách này có phải là danh sách liên kết vòng (phần tử cuối trỏ ngược về ĐÚNG `head`) hay không. (Lưu ý: Không phải chỉ kiểm tra cycle bất kỳ).

**Phân tích thuật toán**:
Duyệt từ `head.next`. Nếu tại bất kỳ điểm nào node bằng `null`, trả về false. Nếu node lặp lại đúng bằng `head`, trả về true.

**Mã nguồn Java**:
```java
class CheckCircular {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public boolean isCircular(Node head) {
        if (head == null) return true; // Cấu trúc rỗng được coi là circular
        
        Node curr = head.next;
        while (curr != null && curr != head) {
            curr = curr.next;
        }
        
        return (curr == head);
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$.
- Không gian: $O(1)$.

---

## 7. Convert Singly Linked List to Circular

**Đề bài chi tiết**:
Cho một Singly Linked List bình thường (phần tử cuối trỏ tới `null`), hãy chuyển nó thành Circular Linked List.

**Phân tích thuật toán**:
Duyệt danh sách để tìm phần tử cuối cùng (`tail`), sau đó gán `tail.next = head`.

**Mã nguồn Java**:
```java
class ConvertToCircular {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public Node convert(Node head) {
        if (head == null) return null;
        
        Node curr = head;
        while (curr.next != null) {
            curr = curr.next;
        }
        
        curr.next = head; // Link tail back to head
        return head;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$.
- Không gian: $O(1)$.

---

## 8. Delete a Node from Circular Linked List

**Đề bài chi tiết**:
Cho `head` của Circular Linked List và một giá trị `key`. Xóa Node đầu tiên chứa giá trị `key`.

**Phân tích thuật toán**:
Duyệt tìm Node có giá trị bằng `key` cùng với Node kề trước (`prev`).
- Nếu list trống, return null.
- Nếu danh sách chỉ có 1 Node và cần xóa, return null.
- Nếu Node cần xóa là `head`, phải tìm `tail` để gán lại `tail.next = head.next`, rồi dời `head`.
- Các trường hợp khác, `prev.next = curr.next`.

**Mã nguồn Java**:
```java
class DeleteNodeCircular {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public Node deleteNode(Node head, int key) {
        if (head == null) return null;
        
        Node curr = head;
        Node prev = null;
        
        // Find the node
        while (curr.val != key) {
            if (curr.next == head) {
                return head; // Key not found
            }
            prev = curr;
            curr = curr.next;
        }
        
        // Only one node in the list
        if (curr == head && curr.next == head) {
            return null;
        }
        
        // If head is to be deleted
        if (curr == head) {
            prev = head;
            while (prev.next != head) {
                prev = prev.next;
            }
            head = curr.next;
            prev.next = head;
        } 
        // If intermediate or tail node is to be deleted
        else {
            prev.next = curr.next;
        }
        
        return head;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$.
- Không gian: $O(1)$.

---

## 9. Count nodes in Circular linked list

**Đề bài chi tiết**:
Tính số lượng phần tử trong một Circular Linked List cho trước.

**Phân tích thuật toán**:
Dùng `do-while` để duyệt qua các phần tử từ `head`, tăng biến đếm ở mỗi bước cho đến khi gặp lại `head`.

**Mã nguồn Java**:
```java
class CountNodesCircular {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public int countNodes(Node head) {
        if (head == null) return 0;
        
        int count = 0;
        Node curr = head;
        do {
            count++;
            curr = curr.next;
        } while (curr != head);
        
        return count;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$.
- Không gian: $O(1)$.

---

## 10. Exchange first and last nodes in Circular Linked List

**Đề bài chi tiết**:
Cho `head` của một Circular Linked List. Hãy hoán đổi vị trí (đổi trỏ link, không đổi giá trị) của Node đầu tiên và Node cuối cùng.

**Phân tích thuật toán**:
Xác định `head`, Node thứ 2, Node kề cuối (prev of tail), và `tail`.
Trường hợp đặc biệt: Danh sách có ít hơn 3 phần tử thì tự xử lý dễ dàng.
Nếu >= 3 phần tử:
- `tail.next = head.next`
- `prevOfTail.next = head`
- `head.next = tail` (nhưng nhớ list là vòng nên `head.next` lúc này trỏ vào `tail`, `tail` đóng vai trò là `head` mới, `head` cũ đóng vai trò là `tail` mới).

**Mã nguồn Java**:
```java
class ExchangeFirstLast {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public Node exchangeNodes(Node head) {
        if (head == null || head.next == head) return head;
        
        // Only 2 nodes
        if (head.next.next == head) {
            return head.next; 
        }
        
        Node prev = null;
        Node curr = head;
        
        // Find tail and its previous node
        while (curr.next != head) {
            prev = curr;
            curr = curr.next;
        }
        
        // curr is tail, prev is prevOfTail
        Node temp = head.next; // Node 2
        
        // Update links
        curr.next = temp;      // Tail next points to node 2
        prev.next = head;      // Prev points to old head (new tail)
        head.next = curr;      // Old head points to new head (old tail) to close loop
        
        return curr; // Return new head
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$.
- Không gian: $O(1)$.

---

## 11. Reverse a Circular Linked List

**Đề bài chi tiết**:
Cho một Circular Linked List. Hãy đảo ngược thứ tự các Node trong danh sách này.

**Phân tích thuật toán**:
Dùng 3 con trỏ `prev`, `curr`, `next` giống như đảo ngược danh sách liên kết đơn. Cần lưu lại `head` ban đầu. Duyệt qua danh sách bằng vòng lặp `do-while` (hoặc `while (curr != head)` sau bước khởi tạo), đảo ngược từng liên kết `curr.next = prev`. Sau khi vòng lặp kết thúc, cập nhật `head.next = prev` (để kết nối lại thành vòng) và trả về `prev` làm `head` mới.

**Mã nguồn Java**:
```java
class ReverseCircular {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public Node reverse(Node head) {
        if (head == null || head.next == head) return head;
        
        Node prev = head;
        Node curr = head.next;
        Node next;
        
        while (curr != head) {
            next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        
        // Kết nối lại node đầu tiên (giờ là node cuối) với node cuối cùng (giờ là node đầu)
        head.next = prev;
        return prev;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$.
- Không gian: $O(1)$.

---

## 12. Delete all occurrences of a key

**Đề bài chi tiết**:
Xóa tất cả các Node có giá trị bằng `key` cho trước trong một Circular Linked List.

**Phân tích thuật toán**:
Trước tiên, tìm `tail` của danh sách. Sau đó, xử lý trường hợp `head` chứa giá trị `key` bằng cách dời `head` và cập nhật `tail.next = head`. Tiếp tục duyệt qua các Node còn lại bằng một con trỏ `curr` và `prev` để xóa các Node chứa `key` bằng cách gán `prev.next = curr.next`. Cần đặc biệt lưu ý trường hợp tất cả các Node đều chứa `key` (danh sách sẽ bị xóa sạch).

**Mã nguồn Java**:
```java
class DeleteAllOccurrences {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public Node deleteAll(Node head, int key) {
        if (head == null) return null;
        
        Node tail = head;
        while (tail.next != head) {
            tail = tail.next;
        }
        
        Node curr = head;
        // Nếu head chứa key, dời head
        while (curr.next != head && curr.val == key) {
            tail.next = curr.next;
            head = curr.next;
            curr = head;
        }
        
        // Trường hợp tất cả các node đều chứa key
        if (curr.val == key && curr.next == head) {
            return null;
        }
        
        // Đã đảm bảo head mới không chứa key
        Node prev = head;
        curr = head.next;
        while (curr != head) {
            if (curr.val == key) {
                prev.next = curr.next;
            } else {
                prev = curr;
            }
            curr = curr.next;
        }
        
        return head;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$.
- Không gian: $O(1)$.

---

## 13. Find Middle Node

**Đề bài chi tiết**:
Tìm phần tử ở giữa của một Circular Linked List. Nếu danh sách có số lượng Node chẵn, trả về Node thứ hai của nửa sau.

**Phân tích thuật toán**:
Sử dụng kỹ thuật Rùa và Thỏ (Slow & Fast pointers). Khởi tạo `slow` và `fast` cùng trỏ vào `head`. `slow` nhảy 1 bước, `fast` nhảy 2 bước. Lặp lại cho đến khi `fast.next == head` (đối với số Node lẻ) hoặc `fast.next.next == head` (đối với số Node chẵn). Nếu chẵn, có thể cần trả về `slow.next` tùy theo quy ước bài toán.

**Mã nguồn Java**:
```java
class FindMiddleCircular {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public Node findMiddle(Node head) {
        if (head == null) return null;
        
        Node slow = head;
        Node fast = head;
        
        while (fast.next != head && fast.next.next != head) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        if (fast.next.next == head) {
            return slow.next; // Trả về node thứ hai nếu độ dài chẵn
        }
        
        return slow;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$.
- Không gian: $O(1)$.

---

## 14. Check if Doubly Linked List is Circular

**Đề bài chi tiết**:
Kiểm tra xem một danh sách liên kết kép (Doubly Linked List) có phải là danh sách vòng (Circular Doubly Linked List) hay không.

**Phân tích thuật toán**:
Bắt đầu từ `head`, nếu `head == null` coi như là vòng (hoặc không tùy định nghĩa, ở đây coi là true). Duyệt theo con trỏ `next` cho đến khi gặp lại `head` hoặc gặp `null`. Nếu gặp `head` thông qua `curr.next`, đồng thời `head.prev == curr` (Node cuối cùng), thì đó là Circular Doubly Linked List hợp lệ.

**Mã nguồn Java**:
```java
class CheckCircularDoubly {
    static class DNode {
        int val;
        DNode prev, next;
        DNode(int val) { this.val = val; }
    }

    public boolean isCircularDoubly(DNode head) {
        if (head == null) return true;
        
        DNode curr = head;
        while (curr.next != null && curr.next != head) {
            curr = curr.next;
        }
        
        return curr.next == head && head.prev == curr;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$.
- Không gian: $O(1)$.

---

## 15. Convert Doubly to Circular Doubly Linked List

**Đề bài chi tiết**:
Chuyển một danh sách liên kết kép (Doubly Linked List) thông thường (node cuối trỏ `null`) thành danh sách liên kết kép vòng (Circular Doubly Linked List).

**Phân tích thuật toán**:
Nếu danh sách rỗng, trả về `null`. Nếu không, ta chỉ cần tìm phần tử cuối cùng `tail` bằng cách duyệt theo `next`. Sau đó, thiết lập liên kết vòng: gán `tail.next = head` và `head.prev = tail`.

**Mã nguồn Java**:
```java
class ConvertDoublyToCircular {
    static class DNode {
        int val;
        DNode prev, next;
        DNode(int val) { this.val = val; }
    }

    public DNode convert(DNode head) {
        if (head == null) return null;
        
        DNode tail = head;
        while (tail.next != null) {
            tail = tail.next;
        }
        
        tail.next = head;
        head.prev = tail;
        
        return head;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$.
- Không gian: $O(1)$.

---

## 16. Remove Every K-th Node

**Đề bài chi tiết**:
Xóa mọi Node thứ K trong một Circular Linked List cho đến khi danh sách trở nên rỗng.

**Phân tích thuật toán**:
Sử dụng một biến đếm và duyệt liên tục vòng tròn. Ở mỗi bước thứ $K$, cập nhật con trỏ `next` của Node kề trước để loại bỏ Node hiện tại khỏi vòng. Nếu Node bị loại bỏ là `head`, cần cập nhật lại `head`. Nếu $K=1$, ta xóa sạch danh sách bằng cách trả về `null`.

**Mã nguồn Java**:
```java
class RemoveKthNode {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public Node removeEveryKth(Node head, int k) {
        if (head == null) return null;
        if (k == 1) return null; // K = 1 thì xoá tất cả
        
        Node curr = head;
        Node prev = null;
        
        // Tìm prev ban đầu là tail
        while (curr.next != head) {
            curr = curr.next;
        }
        prev = curr;
        curr = head;
        
        while (curr.next != curr) {
            // Nhảy k-1 bước
            for (int i = 0; i < k - 1; i++) {
                prev = curr;
                curr = curr.next;
            }
            
            // Xoá node curr
            prev.next = curr.next;
            if (curr == head) {
                head = prev.next;
            }
            curr = prev.next;
        }
        
        return null; // Node cuối cùng cũng bị xoá
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N \times K)$.
- Không gian: $O(1)$.

---

## 17. Concatenate Two Circular Linked Lists

**Đề bài chi tiết**:
Nối hai Circular Linked Lists lại với nhau thành một danh sách vòng duy nhất.

**Phân tích thuật toán**:
Nếu một trong hai danh sách rỗng, trả về danh sách kia. Nếu cả hai đều không rỗng, ta duyệt tìm `tail1` của danh sách thứ nhất và `tail2` của danh sách thứ hai. Sau đó, nối chéo lại: gán `tail1.next = head2` và `tail2.next = head1`. Trả về `head1` làm gốc.

**Mã nguồn Java**:
```java
class ConcatenateCircular {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public Node concatenate(Node head1, Node head2) {
        if (head1 == null) return head2;
        if (head2 == null) return head1;
        
        Node tail1 = head1;
        while (tail1.next != head1) {
            tail1 = tail1.next;
        }
        
        Node tail2 = head2;
        while (tail2.next != head2) {
            tail2 = tail2.next;
        }
        
        tail1.next = head2;
        tail2.next = head1;
        
        return head1;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N + M)$ với $N, M$ là số lượng phần tử của 2 danh sách.
- Không gian: $O(1)$.

---

## 18. Find Length of Loop in Linked List

**Đề bài chi tiết**:
Cho một Singly Linked List có thể chứa chu trình (loop). Nếu có chu trình, hãy tìm số lượng Node nằm trong vòng lặp đó. Nếu không có, trả về 0.

**Phân tích thuật toán**:
Dùng kỹ thuật Slow và Fast pointers để phát hiện chu trình. Nếu chúng gặp nhau tại một Node `P`, điều đó chứng tỏ có chu trình. Cố định `P`, dùng một con trỏ chạy tiếp quanh chu trình và đếm cho đến khi quay lại gặp chính `P`. Đó là độ dài của chu trình.

**Mã nguồn Java**:
```java
class LoopLength {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public int countNodesInLoop(Node head) {
        Node slow = head;
        Node fast = head;
        
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            
            // Phát hiện chu trình
            if (slow == fast) {
                return countNodes(slow);
            }
        }
        
        return 0;
    }
    
    private int countNodes(Node match) {
        int count = 1;
        Node temp = match;
        while (temp.next != match) {
            count++;
            temp = temp.next;
        }
        return count;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$.
- Không gian: $O(1)$.

---

## 19. Sort a Circular Linked List

**Đề bài chi tiết**:
Sắp xếp một Circular Linked List theo thứ tự tăng dần bằng thuật toán Merge Sort.

**Phân tích thuật toán**:
Do danh sách là vòng, trước hết ta ngắt liên kết vòng tại `tail` để biến nó thành Singly Linked List thông thường. Áp dụng Merge Sort đệ quy bằng cách chia đôi danh sách (sử dụng Fast & Slow pointers) và trộn (Merge) hai danh sách con đã sắp xếp. Sau khi sắp xếp xong, ta tìm lại `tail` của danh sách kết quả và nối nó về `head` để tạo lại vòng.

**Mã nguồn Java**:
```java
class SortCircular {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public Node sort(Node head) {
        if (head == null || head.next == head) return head;
        
        // Ngắt vòng
        Node tail = head;
        while (tail.next != head) {
            tail = tail.next;
        }
        tail.next = null;
        
        // Gọi hàm Merge Sort
        head = mergeSort(head);
        
        // Khôi phục lại vòng
        tail = head;
        while (tail.next != null) {
            tail = tail.next;
        }
        tail.next = head;
        
        return head;
    }
    
    private Node mergeSort(Node head) {
        if (head == null || head.next == null) return head;
        Node middle = getMiddle(head);
        Node nextOfMiddle = middle.next;
        middle.next = null;
        
        Node left = mergeSort(head);
        Node right = mergeSort(nextOfMiddle);
        
        return merge(left, right);
    }
    
    private Node getMiddle(Node head) {
        if (head == null) return head;
        Node slow = head, fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
    
    private Node merge(Node a, Node b) {
        if (a == null) return b;
        if (b == null) return a;
        Node result;
        if (a.val <= b.val) {
            result = a;
            result.next = merge(a.next, b);
        } else {
            result = b;
            result.next = merge(a, b.next);
        }
        return result;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N \log N)$.
- Không gian: $O(\log N)$ do call stack đệ quy của Merge Sort.

---

## 20. Insert at the Beginning of Circular List (using Head)

**Đề bài chi tiết**:
Chèn một Node mới với giá trị `val` vào đầu của Circular Linked List. Hàm chỉ được cung cấp con trỏ `head`.

**Phân tích thuật toán**:
Tạo một `newNode`. Nếu danh sách rỗng, gán `newNode.next = newNode` và trả về nó làm `head`. Nếu danh sách không rỗng, ta phải duyệt đến phần tử cuối cùng `tail` vì `tail` cần trỏ ngược về `newNode`. Gán `newNode.next = head`, sau đó `tail.next = newNode`. Trả về `newNode` làm gốc mới của danh sách.

**Mã nguồn Java**:
```java
class InsertBeginCircular {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public Node insertBegin(Node head, int val) {
        Node newNode = new Node(val);
        if (head == null) {
            newNode.next = newNode;
            return newNode;
        }
        
        Node tail = head;
        while (tail.next != head) {
            tail = tail.next;
        }
        
        newNode.next = head;
        tail.next = newNode;
        
        return newNode; // Node mới trở thành head
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$ do phải tìm `tail`.
- Không gian: $O(1)$.

---

## 21. Remove Alternate Nodes in Circular List

**Đề bài chi tiết**:
Cho một Circular Linked List. Hãy xóa các Node ở vị trí xen kẽ (ví dụ giữ lại Node 1, xóa Node 2, giữ Node 3, xóa Node 4, v.v.) cho đến khi đi hết một vòng danh sách.

**Phân tích thuật toán**:
Sử dụng một con trỏ `curr` bắt đầu từ `head`. Nếu danh sách rỗng hoặc chỉ có 1 Node thì không cần làm gì. Với danh sách có nhiều hơn 1 Node, ở mỗi bước ta cập nhật `curr.next = curr.next.next` để bỏ qua Node kề tiếp, sau đó di chuyển `curr = curr.next`. Vòng lặp kết thúc khi `curr` quay lại `head` (đối với danh sách lẻ Node) hoặc `curr.next` quay lại `head` (đối với danh sách chẵn Node).

**Mã nguồn Java**:
```java
class RemoveAlternate {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public void removeAlternate(Node head) {
        if (head == null || head.next == head) return;
        
        Node curr = head;
        do {
            // Xóa node tiếp theo
            curr.next = curr.next.next;
            // Di chuyển curr sang node kế tiếp (đã là node an toàn)
            curr = curr.next;
        } while (curr != head && curr.next != head);
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$ để duyệt qua nửa số Node của danh sách.
- Không gian: $O(1)$.

---

## 22. Detect Cycle using HashMap

**Đề bài chi tiết**:
Kiểm tra xem một danh sách liên kết có chứa chu trình (cycle) hay không bằng cách sử dụng `HashMap` hoặc `HashSet`. Trả về `true` nếu có, `false` nếu không.

**Phân tích thuật toán**:
Khởi tạo một `HashSet` để lưu trữ các đối tượng `Node` đã duyệt qua. Duyệt từ `head`, với mỗi Node, kiểm tra xem nó đã tồn tại trong `HashSet` chưa. Nếu rồi, chứng tỏ danh sách có chu trình, trả về `true`. Nếu chưa, thêm nó vào `HashSet` và di chuyển sang Node tiếp theo. Nếu duyệt gặp `null` thì trả về `false`.

**Mã nguồn Java**:
```java
import java.util.HashSet;

class DetectCycleHashSet {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public boolean hasCycle(Node head) {
        HashSet<Node> visited = new HashSet<>();
        Node curr = head;
        
        while (curr != null) {
            if (visited.contains(curr)) {
                return true;
            }
            visited.add(curr);
            curr = curr.next;
        }
        
        return false;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$ vì mỗi Node thêm vào HashSet mất $O(1)$ trung bình.
- Không gian: $O(N)$ để lưu trữ tối đa $N$ Node trong HashSet.

---

## 23. Clone a Circular Linked List

**Đề bài chi tiết**:
Tạo một bản sao chép sâu (deep copy) của một Circular Linked List. Danh sách mới phải có các Node hoàn toàn độc lập với danh sách cũ nhưng mang giá trị và cấu trúc tương tự.

**Phân tích thuật toán**:
Nếu danh sách gốc rỗng, trả về `null`. Khởi tạo `newHead` bằng giá trị của `head` gốc. Dùng một con trỏ `tail` để giữ cuối danh sách mới và một con trỏ `curr` chạy từ `head.next` của danh sách gốc. Trong vòng lặp, liên tục tạo `newNode`, gắn vào `tail.next`, rồi di chuyển `tail`. Khi `curr` quay về `head`, nối `tail.next = newHead` để đóng vòng.

**Mã nguồn Java**:
```java
class CloneCircular {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public Node cloneList(Node head) {
        if (head == null) return null;
        
        Node newHead = new Node(head.val);
        Node tail = newHead;
        Node curr = head.next;
        
        while (curr != head) {
            tail.next = new Node(curr.val);
            tail = tail.next;
            curr = curr.next;
        }
        
        // Đóng vòng bản sao
        tail.next = newHead;
        return newHead;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$.
- Không gian: $O(N)$ cho danh sách bản sao mới.

---

## 24. Merge two Sorted Circular Linked Lists

**Đề bài chi tiết**:
Gộp hai Circular Linked Lists đã được sắp xếp tăng dần thành một Circular Linked List duy nhất, kết quả vẫn phải giữ nguyên tính chất sắp xếp tăng dần.

**Phân tích thuật toán**:
Cách đơn giản nhất là chuyển cả hai thành Singly Linked List (bằng cách ngắt liên kết ở `tail`). Sau đó, áp dụng thuật toán gộp (Merge) hai Singly Linked List đã sắp xếp. Cuối cùng, tìm Node cuối cùng của danh sách kết quả và nối ngược về `head` để tạo vòng.

**Mã nguồn Java**:
```java
class MergeSortedCircular {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public Node merge(Node head1, Node head2) {
        if (head1 == null) return head2;
        if (head2 == null) return head1;
        
        // Ngắt vòng list 1
        Node tail1 = head1;
        while (tail1.next != head1) tail1 = tail1.next;
        tail1.next = null;
        
        // Ngắt vòng list 2
        Node tail2 = head2;
        while (tail2.next != head2) tail2 = tail2.next;
        tail2.next = null;
        
        // Gộp như Singly Linked List
        Node dummy = new Node(0);
        Node curr = dummy;
        Node p1 = head1, p2 = head2;
        
        while (p1 != null && p2 != null) {
            if (p1.val <= p2.val) {
                curr.next = p1;
                p1 = p1.next;
            } else {
                curr.next = p2;
                p2 = p2.next;
            }
            curr = curr.next;
        }
        
        if (p1 != null) curr.next = p1;
        if (p2 != null) curr.next = p2;
        
        // Khôi phục vòng cho danh sách kết quả
        Node mergedHead = dummy.next;
        Node mergedTail = mergedHead;
        while (mergedTail.next != null) {
            mergedTail = mergedTail.next;
        }
        mergedTail.next = mergedHead;
        
        return mergedHead;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N + M)$ để duyệt cả hai danh sách.
- Không gian: $O(1)$.

---

## 25. Add polynomial represented as Circular Linked List

**Đề bài chi tiết**:
Biểu diễn hai đa thức dưới dạng Circular Linked List (mỗi Node chứa hệ số `coeff` và số mũ `power`). Viết hàm cộng hai đa thức này và trả về kết quả dưới dạng Circular Linked List mới. (Giả sử các Node đã sắp xếp giảm dần theo số mũ).

**Phân tích thuật toán**:
Sử dụng hai con trỏ `p1` và `p2` để duyệt 2 đa thức. Tạo danh sách kết quả mới. Lần lượt so sánh `power` của `p1` và `p2`:
- Nếu `power` bằng nhau, cộng hệ số, nếu tổng khác 0 thì tạo Node mới cho kết quả. Di chuyển cả `p1` và `p2`.
- Nếu `p1.power > p2.power`, thêm Node `p1` vào kết quả, di chuyển `p1`.
- Ngược lại thêm Node `p2` vào kết quả, di chuyển `p2`.
Để đơn giản hóa vòng lặp, có thể ngắt vòng thành Singly List trước rồi khôi phục sau.

**Mã nguồn Java**:
```java
class AddPolynomial {
    static class Node {
        int coeff, power;
        Node next;
        Node(int c, int p) { coeff = c; power = p; }
    }

    public Node addPolynomials(Node head1, Node head2) {
        if (head1 == null) return clonePoly(head2);
        if (head2 == null) return clonePoly(head1);
        
        Node tail1 = head1; while(tail1.next != head1) tail1 = tail1.next; tail1.next = null;
        Node tail2 = head2; while(tail2.next != head2) tail2 = tail2.next; tail2.next = null;
        
        Node dummy = new Node(0, 0);
        Node curr = dummy;
        Node p1 = head1, p2 = head2;
        
        while (p1 != null && p2 != null) {
            if (p1.power > p2.power) {
                curr.next = new Node(p1.coeff, p1.power);
                p1 = p1.next;
            } else if (p1.power < p2.power) {
                curr.next = new Node(p2.coeff, p2.power);
                p2 = p2.next;
            } else {
                int sum = p1.coeff + p2.coeff;
                if (sum != 0) {
                    curr.next = new Node(sum, p1.power);
                    curr = curr.next;
                }
                p1 = p1.next; p2 = p2.next;
                continue; // bỏ qua bước curr = curr.next ở dưới do đã tiến curr
            }
            curr = curr.next;
        }
        
        while (p1 != null) { curr.next = new Node(p1.coeff, p1.power); p1 = p1.next; curr = curr.next; }
        while (p2 != null) { curr.next = new Node(p2.coeff, p2.power); p2 = p2.next; curr = curr.next; }
        
        tail1.next = head1; // Khôi phục danh sách gốc
        tail2.next = head2;
        
        Node resHead = dummy.next;
        if (resHead == null) return null;
        
        Node resTail = resHead;
        while (resTail.next != null) resTail = resTail.next;
        resTail.next = resHead; // Đóng vòng kết quả
        
        return resHead;
    }
    
    private Node clonePoly(Node head) {
        // Tương tự bài Clone
        if (head == null) return null;
        Node nHead = new Node(head.coeff, head.power);
        Node t = nHead, c = head.next;
        while(c != null && c != head) {
            t.next = new Node(c.coeff, c.power);
            t = t.next; c = c.next;
        }
        t.next = nHead;
        return nHead;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N + M)$.
- Không gian: $O(N + M)$ để tạo các Node cho đa thức kết quả mới.

---

## 26. Move last element to front in a Circular Linked List

**Đề bài chi tiết**:
Chuyển phần tử cuối cùng của Circular Linked List lên vị trí đầu tiên và trả về `head` mới.

**Phân tích thuật toán**:
Trong Circular Linked List, `tail.next` luôn trỏ về `head`. Để đưa phần tử cuối lên đầu, cấu trúc vật lý của các Node gần như không cần thay đổi! Ta chỉ việc thay đổi khái niệm "đâu là đầu": Node `head` mới chính là `tail` hiện tại. Node kề cuối sẽ trở thành `tail` mới. Ta duyệt một vòng để tìm `tail`, sau đó trả về `tail` làm gốc của danh sách mới.

**Mã nguồn Java**:
```java
class MoveLastToFront {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public Node moveLastToFront(Node head) {
        if (head == null || head.next == head) return head;
        
        Node tail = head;
        while (tail.next != head) {
            tail = tail.next;
        }
        
        // head mới chính là tail cũ
        // tail mới chính là node áp chót, nhưng do đây là list vòng
        // ta không cần tường minh biến đổi con trỏ nào ngoài việc return tail
        // (bởi vì tail.next đã là head cũ)
        return tail;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$ để duyệt tìm `tail`.
- Không gian: $O(1)$.

---

## 27. Intersection of two Circular Linked Lists

**Đề bài chi tiết**:
Tìm Node giao nhau (Intersection Node) của hai Circular Linked Lists nếu có. (Giả sử nếu chúng giao nhau thì chúng sẽ có chung phần đuôi và do đó chung một vòng tròn ở cuối).

**Phân tích thuật toán**:
Tương tự như tìm điểm giao của Singly Linked List. Ta tạm thời ngắt liên kết vòng của cả hai danh sách, biến chúng thành hai Singly List phân biệt. Sử dụng thuật toán 2 con trỏ, duyệt qua danh sách này xong thì chuyển sang danh sách kia. Nếu gặp nhau thì đó là điểm giao. Cuối cùng nối lại vòng cho cả hai để không làm hỏng cấu trúc gốc.

**Mã nguồn Java**:
```java
class IntersectionCircular {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public Node getIntersectionNode(Node headA, Node headB) {
        if (headA == null || headB == null) return null;
        
        // Ngắt vòng A
        Node tailA = headA;
        while (tailA.next != headA) tailA = tailA.next;
        tailA.next = null;
        
        // Ngắt vòng B
        Node tailB = headB;
        while (tailB.next != headB) tailB = tailB.next;
        tailB.next = null;
        
        // Tìm giao điểm
        Node pA = headA;
        Node pB = headB;
        while (pA != pB) {
            pA = (pA == null) ? headB : pA.next;
            pB = (pB == null) ? headA : pB.next;
        }
        
        // Nối lại vòng
        tailA.next = headA;
        tailB.next = headB;
        
        return pA;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N + M)$ với $N, M$ là độ dài hai danh sách.
- Không gian: $O(1)$.

---

## 28. Find Max and Min in a Circular Linked List

**Đề bài chi tiết**:
Tìm giá trị lớn nhất (Max) và nhỏ nhất (Min) trong một Circular Linked List.

**Phân tích thuật toán**:
Khởi tạo biến `max` và `min` với giá trị của `head`. Dùng một vòng lặp `do-while` duyệt qua toàn bộ các Node trong danh sách vòng. Tại mỗi Node, cập nhật `max = Math.max(max, curr.val)` và `min = Math.min(min, curr.val)`. Vòng lặp dừng khi quay lại `head`.

**Mã nguồn Java**:
```java
class FindMaxMin {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public void printMaxMin(Node head) {
        if (head == null) return;
        
        int max = head.val;
        int min = head.val;
        Node curr = head.next;
        
        while (curr != head) {
            if (curr.val > max) max = curr.val;
            if (curr.val < min) min = curr.val;
            curr = curr.next;
        }
        
        System.out.println("Max: " + max + ", Min: " + min);
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$.
- Không gian: $O(1)$.

---

## 29. Remove Duplicates from an Unsorted Circular Linked List

**Đề bài chi tiết**:
Xóa tất cả các Node chứa giá trị trùng lặp trong một Circular Linked List chưa được sắp xếp. Chỉ giữ lại lần xuất hiện đầu tiên của mỗi giá trị.

**Phân tích thuật toán**:
Dùng một `HashSet` để ghi nhớ các giá trị đã gặp. Duyệt danh sách với 2 con trỏ `curr` và `prev`. Đưa `head.val` vào tập hợp. Khi duyệt, nếu `curr.val` đã có trong set, ta loại bỏ `curr` bằng cách gán `prev.next = curr.next`. Nếu chưa có, ta thêm giá trị vào set và dời `prev` lên. Dừng khi duyệt hết vòng.

**Mã nguồn Java**:
```java
import java.util.HashSet;

class RemoveDuplicates {
    static class Node {
        int val;
        Node next;
        Node(int val) { this.val = val; }
    }

    public Node removeDuplicates(Node head) {
        if (head == null || head.next == head) return head;
        
        HashSet<Integer> set = new HashSet<>();
        set.add(head.val);
        
        Node prev = head;
        Node curr = head.next;
        
        while (curr != head) {
            if (set.contains(curr.val)) {
                // Xoá node trùng lặp
                prev.next = curr.next;
            } else {
                set.add(curr.val);
                prev = curr;
            }
            curr = curr.next;
        }
        
        return head;
    }
}
```

**Độ phức tạp**:
- Thời gian: $O(N)$ vì tra cứu trong HashSet tốn $O(1)$.
- Không gian: $O(N)$ để lưu trữ các giá trị độc nhất trong HashSet.

---

## 30. Circular Buffer implementation using Arrays vs Circular Linked List

**Đề bài chi tiết**:
So sánh và cài đặt bộ đệm vòng (Circular Buffer) sử dụng Mảng (Arrays) thay vì Circular Linked List.

**Phân tích thuật toán**:
Bộ đệm vòng bằng mảng sẽ sử dụng một mảng có kích thước cố định `capacity` cùng hai con trỏ `head` và `tail`. 
- Thêm phần tử (Enqueue): Ghi vào vị trí `tail`, cập nhật `tail = (tail + 1) % capacity`.
- Xóa phần tử (Dequeue): Đọc vị trí `head`, cập nhật `head = (head + 1) % capacity`.
So với Linked List, mảng (Array) có lợi thế Cache-friendly, không tốn memory overhead cho đối tượng Node, nhưng bị giới hạn về sức chứa tối đa. Linked List linh hoạt nhưng chậm hơn về mặt cấp phát.

**Mã nguồn Java**:
```java
class CircularBufferArray {
    private int[] buffer;
    private int head;
    private int tail;
    private int size;
    private int capacity;

    public CircularBufferArray(int capacity) {
        this.capacity = capacity;
        this.buffer = new int[capacity];
        this.head = 0;
        this.tail = 0;
        this.size = 0;
    }

    public boolean enqueue(int val) {
        if (isFull()) return false;
        buffer[tail] = val;
        tail = (tail + 1) % capacity;
        size++;
        return true;
    }

    public boolean dequeue() {
        if (isEmpty()) return false;
        head = (head + 1) % capacity;
        size--;
        return true;
    }

    public int getFront() {
        if (isEmpty()) return -1;
        return buffer[head];
    }

    public boolean isEmpty() { return size == 0; }
    public boolean isFull() { return size == capacity; }
}
```

**Độ phức tạp**:
- Thời gian: $O(1)$ cho mọi thao tác.
- Không gian: $O(K)$ với $K$ là `capacity`.
