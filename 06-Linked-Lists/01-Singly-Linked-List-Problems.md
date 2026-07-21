# 01 - Singly Linked List Problems (Bài tập Danh sách liên kết đơn)

## 1. Reverse Linked List (Leetcode 206)
**Đề bài chi tiết:** Cho `head` của một singly linked list, hãy đảo ngược danh sách và trả về danh sách sau khi đã đảo ngược.
**Phân tích thuật toán:** Duyệt qua danh sách, giữ 3 con trỏ `prev`, `curr`, `next`. Tại mỗi bước, trỏ `curr.next` ngược về `prev`, rồi di chuyển tất cả lên 1 bước.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode prev = null;
        ListNode curr = head;
        while (curr != null) {
            ListNode nextTemp = curr.next; // Lưu node tiếp theo
            curr.next = prev;              // Đảo ngược con trỏ
            prev = curr;                   // Dịch prev lên
            curr = nextTemp;               // Dịch curr lên
        }
        return prev;
    }
}
```

## 2. Merge Two Sorted Lists (Leetcode 21)
**Đề bài chi tiết:** Cho hai singly linked lists đã được sắp xếp tăng dần. Hợp nhất hai danh sách này thành một danh sách đã sắp xếp và trả về head.
**Phân tích thuật toán:** Sử dụng `Dummy Head` để dễ thao tác. So sánh từng node của hai lists, nối node nhỏ hơn vào kết quả, đến khi một trong hai list cạn thì nối phần còn lại của list kia vào.
**Độ phức tạp:** Time: O(N + M), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
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

## 3. Remove Nth Node From End of List (Leetcode 19)
**Đề bài chi tiết:** Cho một danh sách liên kết, xóa node thứ `n` tính từ đuôi lên và trả về đầu danh sách.
**Phân tích thuật toán:** Dùng `Dummy Head`. Sử dụng 2 con trỏ `fast` và `slow`. Cho `fast` chạy trước `n` bước, sau đó cả hai cùng chạy. Khi `fast` tới đuôi thì `slow` sẽ đứng ngay trước node cần xóa.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode first = dummy;
        ListNode second = dummy;
        
        for (int i = 1; i <= n + 1; i++) {
            first = first.next;
        }
        while (first != null) {
            first = first.next;
            second = second.next;
        }
        second.next = second.next.next;
        return dummy.next;
    }
}
```

## 4. Remove Linked List Elements (Leetcode 203)
**Đề bài chi tiết:** Xóa tất cả các node trong một singly linked list có `Node.val == val`.
**Phân tích thuật toán:** Sử dụng `Dummy Head` để xử lý dễ dàng nếu node cần xóa nằm ở đầu. Duyệt kiểm tra `curr.next.val`, nếu trùng thì thay đổi liên kết.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode removeElements(ListNode head, int val) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode current = dummy;
        
        while (current.next != null) {
            if (current.next.val == val) {
                current.next = current.next.next;
            } else {
                current = current.next;
            }
        }
        return dummy.next;
    }
}
```

## 5. Odd Even Linked List (Leetcode 328)
**Đề bài chi tiết:** Nhóm tất cả các node có chỉ số vị trí lẻ (1, 3, 5) lại với nhau, theo sau là các node ở vị trí chẵn.
**Phân tích thuật toán:** Dùng hai con trỏ `odd` và `even`. Lưu lại `evenHead`. Tách dần các node vào 2 nhóm, cuối cùng nối `odd.next = evenHead`.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode oddEvenList(ListNode head) {
        if (head == null) return null;
        ListNode odd = head;
        ListNode even = head.next;
        ListNode evenHead = even;
        
        while (even != null && even.next != null) {
            odd.next = even.next;
            odd = odd.next;
            even.next = odd.next;
            even = even.next;
        }
        odd.next = evenHead;
        return head;
    }
}
```

## 6. Swap Nodes in Pairs (Leetcode 24)
**Đề bài chi tiết:** Hoán đổi (Swap) các node kề nhau theo từng cặp trong list và trả về head mới.
**Phân tích thuật toán:** Dùng `Dummy Head`. Sử dụng con trỏ `prev`. Quá trình đổi chỗ: lưu node 1 và node 2, nối `prev` với node 2, node 2 với node 1, node 1 với node kế tiếp.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode current = dummy;
        
        while (current.next != null && current.next.next != null) {
            ListNode first = current.next;
            ListNode second = current.next.next;
            
            // Swapping
            first.next = second.next;
            second.next = first;
            current.next = second;
            
            // Move pointer
            current = first;
        }
        return dummy.next;
    }
}
```

## 7. Reverse Nodes in k-Group (Leetcode 25)
**Đề bài chi tiết:** Đảo ngược list theo các nhóm kích thước `k`. Nếu nhóm cuối cùng có số lượng node ít hơn `k`, giữ nguyên.
**Phân tích thuật toán:** Dùng biến đếm độ dài danh sách. Tính số lượng khối cần đảo. Trong mỗi khối, dùng logic đảo ngược danh sách chuẩn.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if (head == null || k == 1) return head;
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode curr = head, prev = dummy, nxt = dummy;
        int count = 0;
        
        while (curr != null) { curr = curr.next; count++; }
        
        while (count >= k) {
            curr = prev.next;
            nxt = curr.next;
            for (int i = 1; i < k; i++) {
                curr.next = nxt.next;
                nxt.next = prev.next;
                prev.next = nxt;
                nxt = curr.next;
            }
            prev = curr;
            count -= k;
        }
        return dummy.next;
    }
}
```

## 8. Remove Duplicates from Sorted List (Leetcode 83)
**Đề bài chi tiết:** Cho danh sách liên kết đã sắp xếp, xóa tất cả các bản sao để mỗi phần tử chỉ xuất hiện 1 lần.
**Phân tích thuật toán:** Duyệt qua list, nếu `current.val == current.next.val` thì loại bỏ `current.next` bằng cách gán `current.next = current.next.next`.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode current = head;
        while (current != null && current.next != null) {
            if (current.val == current.next.val) {
                current.next = current.next.next;
            } else {
                current = current.next;
            }
        }
        return head;
    }
}
```

## 9. Partition List (Leetcode 86)
**Đề bài chi tiết:** Cho một `x`, phân vùng list sao cho tất cả các node có giá trị `< x` nằm bên trái tất cả các node có giá trị `>= x`. Giữ nguyên thứ tự ban đầu ở mỗi bên.
**Phân tích thuật toán:** Khởi tạo 2 `Dummy Head`: `less` và `greater`. Duyệt qua list và nối mỗi node vào danh sách tương ứng. Cuối cùng, nối đuôi list `less` với đầu list `greater`.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode partition(ListNode head, int x) {
        ListNode lessHead = new ListNode(0);
        ListNode greaterHead = new ListNode(0);
        ListNode less = lessHead;
        ListNode greater = greaterHead;
        
        ListNode curr = head;
        while (curr != null) {
            if (curr.val < x) {
                less.next = curr;
                less = less.next;
            } else {
                greater.next = curr;
                greater = greater.next;
            }
            curr = curr.next;
        }
        greater.next = null;
        less.next = greaterHead.next;
        return lessHead.next;
    }
}
```

## 10. Intersection of Two Linked Lists (Leetcode 160)
**Đề bài chi tiết:** Tìm node giao điểm (nếu có) của 2 danh sách liên kết đơn.
**Phân tích thuật toán:** Khởi tạo hai con trỏ `pA` và `pB`. Khi `pA` tới cuối thì nhảy sang head B, và ngược lại. Cả 2 sẽ đi qua đúng `A + B` chặng đường và gặp nhau ở giao điểm hoặc `null`.
**Độ phức tạp:** Time: O(N + M), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB == null) return null;
        ListNode pA = headA;
        ListNode pB = headB;
        
        while (pA != pB) {
            pA = pA == null ? headB : pA.next;
            pB = pB == null ? headA : pB.next;
        }
        return pA;
    }
}
```

## 11. Palindrome Linked List (Leetcode 234)
**Đề bài chi tiết:** Kiểm tra xem một danh sách liên kết đơn có phải là một chuỗi đối xứng (palindrome) hay không. Trả về `true` nếu đối xứng, ngược lại trả về `false`.
**Phân tích thuật toán:** Sử dụng phương pháp `Fast và Slow Pointers` để tìm điểm giữa của danh sách. Sau đó, đảo ngược nửa sau của danh sách và so sánh từng phần tử của nửa đầu với nửa sau đã đảo ngược. Cuối cùng (tùy chọn nhưng nên làm), khôi phục lại danh sách như ban đầu.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public boolean isPalindrome(ListNode head) {
        if (head == null || head.next == null) return true;
        
        ListNode slow = head, fast = head;
        // Tìm middle
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        // Đảo ngược nửa sau
        ListNode secondHalfHead = reverse(slow.next);
        ListNode p1 = head;
        ListNode p2 = secondHalfHead;
        
        // So sánh
        boolean isPalin = true;
        while (p2 != null) {
            if (p1.val != p2.val) {
                isPalin = false;
                break;
            }
            p1 = p1.next;
            p2 = p2.next;
        }
        
        // Khôi phục lại danh sách
        slow.next = reverse(secondHalfHead);
        return isPalin;
    }
    
    private ListNode reverse(ListNode head) {
        ListNode prev = null;
        ListNode curr = head;
        while (curr != null) {
            ListNode nextTemp = curr.next;
            curr.next = prev;
            prev = curr;
            curr = nextTemp;
        }
        return prev;
    }
}
```

## 12. Linked List Cycle (Leetcode 141)
**Đề bài chi tiết:** Kiểm tra xem danh sách liên kết có chứa chu trình (cycle) hay không. Trả về `true` nếu có chu trình, ngược lại trả về `false`.
**Phân tích thuật toán:** Dùng thuật toán phát hiện chu trình của Floyd (Floyd's Cycle-Finding Algorithm) với hai con trỏ `slow` (đi 1 bước) và `fast` (đi 2 bước). Nếu có vòng lặp, hai con trỏ chắc chắn sẽ gặp nhau.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null || head.next == null) return false;
        
        ListNode slow = head;
        ListNode fast = head;
        
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            
            if (slow == fast) {
                return true;
            }
        }
        
        return false;
    }
}
```

## 13. Linked List Cycle II (Leetcode 142)
**Đề bài chi tiết:** Cho một danh sách liên kết, trả về node bắt đầu của chu trình. Nếu không có chu trình, trả về `null`.
**Phân tích thuật toán:** Tìm chu trình bằng `slow` và `fast`. Khi hai con trỏ gặp nhau, cho một con trỏ chạy từ đầu danh sách (head) và con trỏ kia đi tiếp từ điểm gặp, mỗi bước cùng đi 1 node. Nơi chúng gặp nhau là điểm bắt đầu chu trình.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;
        
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            
            if (slow == fast) {
                ListNode entry = head;
                while (entry != slow) {
                    entry = entry.next;
                    slow = slow.next;
                }
                return entry;
            }
        }
        
        return null;
    }
}
```

## 14. Middle of the Linked List (Leetcode 876)
**Đề bài chi tiết:** Tìm và trả về node ở giữa của một danh sách liên kết đơn. Nếu độ dài chẵn, trả về node thứ hai.
**Phân tích thuật toán:** Dùng hai con trỏ `slow` đi 1 bước, `fast` đi 2 bước. Khi `fast` đi hết danh sách thì `slow` sẽ dừng ở node giữa.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode middleNode(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;
        
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        return slow;
    }
}
```

## 15. Delete Node in a Linked List (Leetcode 237)
**Đề bài chi tiết:** Xóa một node được chỉ định (không phải đuôi) mà không được cung cấp `head` của danh sách.
**Phân tích thuật toán:** Copy giá trị của node liền kề (`node.next.val`) đè lên giá trị của node hiện tại, sau đó chỉnh con trỏ hiện tại trỏ tới node tiếp theo nữa để xóa node liền kề.
**Độ phức tạp:** Time: O(1), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public void deleteNode(ListNode node) {
        node.val = node.next.val;
        node.next = node.next.next;
    }
}
```

## 16. Reorder List (Leetcode 143)
**Đề bài chi tiết:** Sắp xếp lại danh sách `L0 → L1 → … → Ln-1 → Ln` thành `L0 → Ln → L1 → Ln-1 → L2 → Ln-2 → …`.
**Phân tích thuật toán:** Gồm 3 phần: Tìm điểm giữa để cắt làm 2 danh sách, đảo chiều danh sách thứ hai, sau đó trộn đan xen hai danh sách vào nhau.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public void reorderList(ListNode head) {
        if (head == null || head.next == null) return;
        
        // 1. Tìm middle
        ListNode slow = head, fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        // 2. Đảo ngược nửa sau
        ListNode prev = null, curr = slow.next;
        slow.next = null;
        while (curr != null) {
            ListNode nextTemp = curr.next;
            curr.next = prev;
            prev = curr;
            curr = nextTemp;
        }
        
        // 3. Trộn 2 danh sách
        ListNode first = head, second = prev;
        while (second != null) {
            ListNode tmp1 = first.next;
            ListNode tmp2 = second.next;
            
            first.next = second;
            second.next = tmp1;
            
            first = tmp1;
            second = tmp2;
        }
    }
}
```

## 17. Sort List (Leetcode 148)
**Đề bài chi tiết:** Sắp xếp danh sách liên kết tăng dần với bộ nhớ thêm `O(1)` và thời gian `O(N log N)`.
**Phân tích thuật toán:** Dùng `Merge Sort`. Tìm điểm giữa danh sách để chia cắt bằng hàm getMid, đệ quy chia tiếp cho đến khi mỗi đoạn chỉ còn 1 node, cuối cùng dùng hàm `merge` gộp các đoạn đã sắp xếp lại.
**Độ phức tạp:** Time: O(N log N), Space: O(log N) do stack đệ quy.
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) return head;
        
        ListNode mid = getMid(head);
        ListNode left = sortList(head);
        ListNode right = sortList(mid);
        
        return merge(left, right);
    }
    
    private ListNode getMid(ListNode head) {
        ListNode midPrev = null;
        while (head != null && head.next != null) {
            midPrev = (midPrev == null) ? head : midPrev.next;
            head = head.next.next;
        }
        ListNode mid = midPrev.next;
        midPrev.next = null;
        return mid;
    }
    
    private ListNode merge(ListNode list1, ListNode list2) {
        ListNode dummy = new ListNode(0);
        ListNode tail = dummy;
        while (list1 != null && list2 != null) {
            if (list1.val < list2.val) {
                tail.next = list1;
                list1 = list1.next;
            } else {
                tail.next = list2;
                list2 = list2.next;
            }
            tail = tail.next;
        }
        tail.next = (list1 != null) ? list1 : list2;
        return dummy.next;
    }
}
```

## 18. Add Two Numbers (Leetcode 2)
**Đề bài chi tiết:** Cộng hai số không âm lưu trong hai danh sách liên kết theo thứ tự bị ngược (hàng đơn vị ở node đầu).
**Phân tích thuật toán:** Duyệt qua 2 list đồng thời, mỗi bước lấy tổng 2 số và biến nhớ `carry`. Ghi hàng đơn vị của tổng vào list kết quả, lưu trữ lại `carry = sum / 10`.
**Độ phức tạp:** Time: O(max(M, N)), Space: O(max(M, N))
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode dummyHead = new ListNode(0);
        ListNode p = l1, q = l2, curr = dummyHead;
        int carry = 0;
        
        while (p != null || q != null) {
            int x = (p != null) ? p.val : 0;
            int y = (q != null) ? q.val : 0;
            int sum = carry + x + y;
            carry = sum / 10;
            curr.next = new ListNode(sum % 10);
            curr = curr.next;
            if (p != null) p = p.next;
            if (q != null) q = q.next;
        }
        if (carry > 0) {
            curr.next = new ListNode(carry);
        }
        return dummyHead.next;
    }
}
```

## 19. Add Two Numbers II (Leetcode 445)
**Đề bài chi tiết:** Cộng hai số biểu diễn bằng danh sách liên kết theo đúng thứ tự (hàng lớn nhất đứng đầu). Không được phép đảo ngược list.
**Phân tích thuật toán:** Do cần cộng từ hàng đơn vị trở lên (từ cuối mảng), nên đưa tất cả giá trị node vào 2 Stack. Mỗi bước pop dữ liệu ra cộng với nhau, tạo ra các node mới rồi gắn vào phía trước của `head`.
**Độ phức tạp:** Time: O(M + N), Space: O(M + N)
**Mã nguồn Java:**
```java
import java.util.Stack;

public class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        Stack<Integer> s1 = new Stack<>();
        Stack<Integer> s2 = new Stack<>();
        
        while (l1 != null) {
            s1.push(l1.val);
            l1 = l1.next;
        }
        while (l2 != null) {
            s2.push(l2.val);
            l2 = l2.next;
        }
        
        int carry = 0;
        ListNode head = null;
        while (!s1.isEmpty() || !s2.isEmpty() || carry != 0) {
            int x = s1.isEmpty() ? 0 : s1.pop();
            int y = s2.isEmpty() ? 0 : s2.pop();
            int sum = x + y + carry;
            
            ListNode newNode = new ListNode(sum % 10);
            newNode.next = head;
            head = newNode;
            
            carry = sum / 10;
        }
        return head;
    }
}
```

## 20. Copy List with Random Pointer (Leetcode 138)
**Đề bài chi tiết:** Sao chép sâu (deep copy) danh sách có thêm con trỏ `random` có thể trỏ tới bất kì node nào.
**Phân tích thuật toán:** Để tối ưu `O(1)` space: Bước 1) Chèn một clone-node bên cạnh ngay sau mỗi node gốc. Bước 2) Clone `random` trỏ tới `node_gốc.random.next`. Bước 3) Tách lấy clone list.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
/*
// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
*/
public class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) return null;
        
        // 1. Chèn kẹp các node sao chép
        Node curr = head;
        while (curr != null) {
            Node clone = new Node(curr.val);
            clone.next = curr.next;
            curr.next = clone;
            curr = clone.next;
        }
        
        // 2. Cập nhật các con trỏ random
        curr = head;
        while (curr != null) {
            if (curr.random != null) {
                curr.next.random = curr.random.next;
            }
            curr = curr.next.next;
        }
        
        // 3. Tách chuỗi thành hai danh sách
        curr = head;
        Node pseudoHead = new Node(0);
        Node copyCurr = pseudoHead;
        
        while (curr != null) {
            copyCurr.next = curr.next;
            curr.next = curr.next.next; // Khôi phục chuỗi gốc
            copyCurr = copyCurr.next;
            curr = curr.next;
        }
        
        return pseudoHead.next;
    }
}
```

## 21. Insertion Sort List (Leetcode 147)
**Đề bài chi tiết:** Sắp xếp một danh sách liên kết bằng thuật toán Insertion Sort (sắp xếp chèn). Mỗi lần lấy ra 1 node và chèn nó vào đúng vị trí của phần danh sách đã sắp xếp.
**Phân tích thuật toán:** Dùng một `Dummy Head` để trỏ tới danh sách kết quả đã được sắp xếp. Khởi tạo `curr` để duyệt danh sách gốc. Tại mỗi bước, duyệt từ `Dummy Head` để tìm vị trí thích hợp cho `curr`, chèn `curr` vào rồi lặp lại cho tới hết danh sách.
**Độ phức tạp:** Time: O(N^2), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode insertionSortList(ListNode head) {
        if (head == null || head.next == null) return head;
        
        ListNode dummy = new ListNode(0);
        ListNode curr = head;
        
        while (curr != null) {
            ListNode prev = dummy;
            // Tìm vị trí để chèn
            while (prev.next != null && prev.next.val < curr.val) {
                prev = prev.next;
            }
            
            // Chèn node curr vào sau prev
            ListNode nextTemp = curr.next;
            curr.next = prev.next;
            prev.next = curr;
            
            // Di chuyển tới node tiếp theo trong danh sách ban đầu
            curr = nextTemp;
        }
        
        return dummy.next;
    }
}
```

## 22. Remove Duplicates from Sorted List II (Leetcode 82)
**Đề bài chi tiết:** Cho một danh sách liên kết đã sắp xếp tăng dần, hãy xóa TẤT CẢ các node bị trùng lặp, chỉ để lại các node có giá trị xuất hiện duy nhất 1 lần.
**Phân tích thuật toán:** Dùng `Dummy Head` để xử lý dễ dàng trường hợp các node đầu bị trùng lặp và xóa. Duyệt với con trỏ `prev` và `curr`. Nếu `curr` có trùng lặp với `curr.next`, ta lặp qua hết nhóm trùng lặp này, rồi nối `prev.next` tới node nằm ngay sau nhóm đó. Nếu không trùng, chỉ cần đẩy `prev` lên.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode prev = dummy;
        ListNode curr = head;
        
        while (curr != null) {
            // Kiểm tra trùng lặp
            if (curr.next != null && curr.val == curr.next.val) {
                while (curr.next != null && curr.val == curr.next.val) {
                    curr = curr.next; // Lướt qua các node trùng
                }
                prev.next = curr.next; // Bỏ qua toàn bộ phần trùng lặp
            } else {
                prev = prev.next;
            }
            curr = curr.next;
        }
        
        return dummy.next;
    }
}
```

## 23. Reverse Linked List II (Leetcode 92)
**Đề bài chi tiết:** Đảo ngược danh sách liên kết từ vị trí `left` đến vị trí `right`. Trả về danh sách sau khi đã đảo ngược. (Chỉ số 1-indexed).
**Phân tích thuật toán:** Dùng `Dummy Head`. Dịch chuyển con trỏ `prev` đến ngay trước vị trí `left`. Lưu lại `start` (node ở vị trí `left`) và `then` (node ở vị trí `left + 1`). Sử dụng kỹ thuật chèn từng node `then` lên đầu (ngay sau `prev`) trong khoảng từ `left` tới `right`.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        if (head == null || left == right) return head;
        
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode prev = dummy;
        
        for (int i = 0; i < left - 1; i++) {
            prev = prev.next;
        }
        
        ListNode start = prev.next;
        ListNode then = start.next;
        
        // Đảo ngược trong phạm vi (right - left) lần
        for (int i = 0; i < right - left; i++) {
            start.next = then.next;
            then.next = prev.next;
            prev.next = then;
            then = start.next;
        }
        
        return dummy.next;
    }
}
```

## 24. Rotate List (Leetcode 61)
**Đề bài chi tiết:** Xoay danh sách liên kết sang bên phải `k` vị trí.
**Phân tích thuật toán:** Duyệt qua danh sách một lần để lấy chiều dài `L` và biến danh sách thành một vòng tròn bằng cách trỏ `tail.next = head`. Sau đó, ta cần tìm điểm cắt. Vì xoay phải `k` lần, nên node đầu mới sẽ nằm cách đuôi cũ `L - (k % L)` bước. Chạy đến đó, cắt vòng tròn và trả về `head` mới.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        if (head == null || head.next == null || k == 0) return head;
        
        ListNode curr = head;
        int len = 1;
        while (curr.next != null) {
            len++;
            curr = curr.next;
        }
        
        curr.next = head; // Tạo vòng tròn
        
        k = k % len;
        int stepsToNewHead = len - k;
        
        // Tìm node ở vị trí ngay trước đầu mới để cắt
        ListNode newTail = curr;
        while (stepsToNewHead-- > 0) {
            newTail = newTail.next;
        }
        
        ListNode newHead = newTail.next;
        newTail.next = null; // Cắt vòng
        
        return newHead;
    }
}
```

## 25. Flatten a Multilevel Doubly Linked List (Leetcode 430)
**Đề bài chi tiết:** Cho một Doubly Linked List trong đó mỗi node có thêm một con trỏ `child` có thể trỏ tới một DLL con khác. "Làm phẳng" list này thành một danh sách 1 cấp duy nhất, trong đó các node con xuất hiện xen vào giữa list gốc.
**Phân tích thuật toán:** Duyệt qua list, khi gặp một node có `child != null`, ta lấy node kế tiếp (nếu có) cất vào một Stack, sau đó nối `node` với `child` lại như là một `next` thông thường, đồng thời gán `child = null`. Sau khi đi hết nhánh, nối phần tử lấy từ Stack ra để đi tiếp. Dùng Stack rất phù hợp cho duyệt sâu (DFS).
**Độ phức tạp:** Time: O(N), Space: O(N)
**Mã nguồn Java:**
```java
/*
// Definition for a Node.
class Node {
    public int val;
    public Node prev;
    public Node next;
    public Node child;
};
*/
import java.util.Stack;

public class Solution {
    public Node flatten(Node head) {
        if (head == null) return null;
        
        Stack<Node> stack = new Stack<>();
        Node curr = head;
        
        while (curr != null) {
            if (curr.child != null) {
                if (curr.next != null) {
                    stack.push(curr.next); // Lưu node kế tiếp vào stack
                }
                // Nối với child
                curr.next = curr.child;
                curr.next.prev = curr;
                curr.child = null;
            } else if (curr.next == null && !stack.isEmpty()) {
                // Hết đường, lấy từ stack ra để nối
                Node nextNode = stack.pop();
                curr.next = nextNode;
                nextNode.prev = curr;
            }
            curr = curr.next;
        }
        
        return head;
    }
}
```

## 26. Split Linked List in Parts (Leetcode 725)
**Đề bài chi tiết:** Cho một danh sách liên kết độ dài `N`, chia nó thành `k` phần dài xấp xỉ nhau liên tiếp. Chênh lệch độ dài giữa hai phần bất kỳ không được vượt quá 1. Trả về mảng các `head` của từng phần.
**Phân tích thuật toán:** Đếm chiều dài `N`. Mỗi phần sẽ có `N / k` node cơ bản, và `N % k` phần đầu tiên sẽ có thêm 1 node. Cắt danh sách thành `k` đoạn và cất vào mảng kết quả.
**Độ phức tạp:** Time: O(N + k), Space: O(k)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode[] splitListToParts(ListNode head, int k) {
        ListNode[] res = new ListNode[k];
        int len = 0;
        ListNode curr = head;
        while (curr != null) {
            len++;
            curr = curr.next;
        }
        
        int width = len / k;
        int rem = len % k;
        
        curr = head;
        for (int i = 0; i < k; i++) {
            ListNode partHead = curr;
            ListNode prev = null;
            
            int currentPartSize = width + (rem > 0 ? 1 : 0);
            rem--;
            
            for (int j = 0; j < currentPartSize; j++) {
                if (curr != null) {
                    prev = curr;
                    curr = curr.next;
                }
            }
            if (prev != null) {
                prev.next = null; // Cắt danh sách
            }
            res[i] = partHead;
        }
        return res;
    }
}
```

## 27. Linked List Components (Leetcode 817)
**Đề bài chi tiết:** Cho danh sách liên kết và một mảng số nguyên `nums` chứa một tập hợp con các giá trị có trong danh sách. Hãy đếm số lượng các "thành phần liên thông" (connected components) trong list, tức là các chuỗi node liên tiếp đều nằm trong `nums`.
**Phân tích thuật toán:** Chuyển `nums` vào một `HashSet` để tra cứu nhanh `O(1)`. Duyệt list, đếm số lượng đoạn liên tiếp. Mỗi lần thấy một node nằm trong `HashSet` mà node tiếp theo KHÔNG nằm trong (hoặc là node cuối), ta đếm đó là 1 component.
**Độ phức tạp:** Time: O(N + M), Space: O(M) (M là chiều dài `nums`).
**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int numComponents(ListNode head, int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) {
            set.add(num);
        }
        
        int count = 0;
        ListNode curr = head;
        
        while (curr != null) {
            // Nếu node hiện tại thuộc set và (node tiếp theo null HOẶC không thuộc set)
            if (set.contains(curr.val) && (curr.next == null || !set.contains(curr.next.val))) {
                count++;
            }
            curr = curr.next;
        }
        
        return count;
    }
}
```

## 28. Next Greater Node In Linked List (Leetcode 1019)
**Đề bài chi tiết:** Trả về một mảng nguyên chứa "Next Greater Node" cho tất cả các node trong list. Nếu một node không có node phía sau nào lớn hơn nó, giá trị trả về là 0.
**Phân tích thuật toán:** Chuyển list thành list mảng để dễ truy cập chỉ số. Sử dụng `Monotonic Stack` (Stack giảm dần) để lưu trữ chỉ số của các node chưa tìm được node lớn hơn nó. Khi duyệt qua mảng, nếu gặp một giá trị lớn hơn giá trị của chỉ số trên đỉnh stack, ta đã tìm được kết quả cho index ở đỉnh.
**Độ phức tạp:** Time: O(N), Space: O(N)
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class Solution {
    public int[] nextLargerNodes(ListNode head) {
        List<Integer> list = new ArrayList<>();
        ListNode curr = head;
        while (curr != null) {
            list.add(curr.val);
            curr = curr.next;
        }
        
        int n = list.size();
        int[] res = new int[n];
        Stack<Integer> stack = new Stack<>(); // Lưu index
        
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && list.get(stack.peek()) < list.get(i)) {
                res[stack.pop()] = list.get(i);
            }
            stack.push(i);
        }
        
        return res;
    }
}
```

## 29. Merge In Between Linked Lists (Leetcode 1669)
**Đề bài chi tiết:** Cho `list1` và `list2`. Xóa các node trong `list1` từ chỉ số `a` đến `b` (tính từ 0) và thay thế vào đó toàn bộ `list2`.
**Phân tích thuật toán:** Duyệt `list1` để tìm node tại vị trí `a - 1` (gọi là `prevA`) và node tại vị trí `b + 1` (gọi là `afterB`). Nối `prevA.next` vào `head` của `list2`, sau đó duyệt tìm đuôi của `list2` và nối nó vào `afterB`.
**Độ phức tạp:** Time: O(N + M), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode mergeInBetween(ListNode list1, int a, int b, ListNode list2) {
        ListNode prevA = list1;
        for (int i = 0; i < a - 1; i++) {
            prevA = prevA.next;
        }
        
        ListNode afterB = prevA.next;
        for (int i = 0; i < b - a + 1; i++) {
            afterB = afterB.next;
        }
        
        // Nối
        prevA.next = list2;
        ListNode tail2 = list2;
        while (tail2.next != null) {
            tail2 = tail2.next;
        }
        tail2.next = afterB;
        
        return list1;
    }
}
```

## 30. Swapping Nodes in a Linked List (Leetcode 1721)
**Đề bài chi tiết:** Hoán đổi GIÁ TRỊ (không hoán đổi node) của node thứ `k` tính từ đầu và node thứ `k` tính từ cuối (Chỉ số 1-indexed).
**Phân tích thuật toán:** Tìm node thứ `k` tính từ đầu (gọi là `firstK`). Dùng 2 con trỏ, con trỏ `fast` tiếp tục chạy từ `firstK` đến cuối, con trỏ `slow` chạy từ đầu danh sách, khi `fast` chạm cuối thì `slow` chính là node thứ `k` từ cuối (gọi là `secondK`). Hoán đổi `val` của chúng.
**Độ phức tạp:** Time: O(N), Space: O(1)
**Mã nguồn Java:**
```java
public class Solution {
    public ListNode swapNodes(ListNode head, int k) {
        ListNode fast = head;
        // Đi k - 1 bước
        for (int i = 1; i < k; i++) {
            fast = fast.next;
        }
        
        ListNode firstK = fast; // node thứ k từ đầu
        ListNode slow = head;
        
        while (fast.next != null) {
            fast = fast.next;
            slow = slow.next;
        }
        
        ListNode secondK = slow; // node thứ k từ cuối
        
        // Hoán đổi giá trị
        int temp = firstK.val;
        firstK.val = secondK.val;
        secondK.val = temp;
        
        return head;
    }
}
```
