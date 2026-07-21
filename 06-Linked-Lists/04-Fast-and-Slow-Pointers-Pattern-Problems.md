# 04. Fast and Slow Pointers Pattern - Problems

Bài viết này cung cấp 30 bài tập về mẫu thuật toán Con trỏ Nhanh và Chậm (Fast and Slow Pointers Pattern). Trong đó, 10 bài đầu tiên được trình bày đầy đủ chi tiết bao gồm đề bài, phân tích, mã nguồn Java 21 và độ phức tạp. 20 bài còn lại được tóm tắt ý tưởng áp dụng.

---

## 1. Linked List Cycle (LeetCode 141)

### Đề bài chi tiết
Cho đầu của một danh sách liên kết `head`, xác định xem danh sách đó có chứa chu trình (cycle) hay không. Có một chu trình nếu có bất kỳ node nào có thể được truy cập lại bằng cách liên tục theo dõi con trỏ `next`. Trả về `true` nếu có chu trình, ngược lại `false`.

### Phân tích thuật toán
Sử dụng 2 con trỏ: `slow` đi 1 bước, `fast` đi 2 bước. Nếu có chu trình, `fast` sẽ quay lại và đuổi kịp `slow`. Nếu `fast` đi tới `null`, nghĩa là không có chu trình.

### Mã nguồn Java
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

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(1)$

---

## 2. Linked List Cycle II (LeetCode 142)

### Đề bài chi tiết
Cho danh sách liên kết `head`, trả về node mà chu trình bắt đầu. Nếu không có chu trình, trả về `null`. Không được thay đổi danh sách gốc.

### Phân tích thuật toán
Đầu tiên, dùng Fast/Slow pointer để tìm điểm gặp nhau trong chu trình. Sau khi hai con trỏ gặp nhau, ta giữ một con trỏ ở điểm gặp, và di chuyển con trỏ còn lại về `head`. Di chuyển cả hai con trỏ 1 bước mỗi lần. Điểm chúng gặp nhau lần thứ 2 chính là Node bắt đầu chu trình (dựa trên thuật toán Floyd).

### Mã nguồn Java
```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode slow = head, fast = head;
        
        // 1. Tìm điểm giao nhau
        boolean hasCycle = false;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast) {
                hasCycle = true;
                break;
            }
        }
        
        if (!hasCycle) return null;
        
        // 2. Tìm điểm bắt đầu
        slow = head;
        while (slow != fast) {
            slow = slow.next;
            fast = fast.next;
        }
        
        return slow; // Hoặc fast, vì cả 2 đang đứng ở điểm bắt đầu
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(1)$

---

## 3. Middle of the Linked List (LeetCode 876)

### Đề bài chi tiết
Cho đầu của danh sách liên kết `head`, trả về node ở giữa. Nếu danh sách có số lượng node chẵn, trả về node giữa thứ hai (thiên về bên phải).

### Phân tích thuật toán
Sử dụng `slow` (đi 1 bước) và `fast` (đi 2 bước). Khi `fast` chạm đến cuối danh sách (hoặc `null`), `slow` sẽ ở vị trí chính giữa vì vận tốc `fast` gấp đôi `slow`.

### Mã nguồn Java
```java
public class Solution {
    public ListNode middleNode(ListNode head) {
        ListNode slow = head, fast = head;
        
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        return slow;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(1)$

---

## 4. Palindrome Linked List (LeetCode 234)

### Đề bài chi tiết
Cho danh sách liên kết đơn `head`, trả về `true` nếu các giá trị của danh sách tạo thành một chuỗi đối xứng (Palindrome), ngược lại trả về `false`.

### Phân tích thuật toán
1. Tìm điểm giữa dùng Fast/Slow.
2. Đảo ngược nửa sau của danh sách (từ `slow.next`).
3. So sánh nửa đầu và nửa sau.
4. (Tùy chọn) Phục hồi lại nửa sau để không phá hỏng dữ liệu gốc.

### Mã nguồn Java
```java
public class Solution {
    public boolean isPalindrome(ListNode head) {
        if (head == null || head.next == null) return true;
        
        // 1. Tìm điểm giữa
        ListNode slow = head, fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        // 2. Đảo ngược nửa sau
        ListNode secondHalf = reverse(slow.next);
        ListNode firstHalf = head;
        ListNode copySecond = secondHalf; // Dùng để phục hồi
        
        // 3. So sánh
        boolean isPalin = true;
        while (secondHalf != null) {
            if (firstHalf.val != secondHalf.val) {
                isPalin = false;
                break;
            }
            firstHalf = firstHalf.next;
            secondHalf = secondHalf.next;
        }
        
        // Phục hồi lại list (Best practice)
        slow.next = reverse(copySecond);
        
        return isPalin;
    }
    
    private ListNode reverse(ListNode head) {
        ListNode prev = null, curr = head;
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

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(1)$

---

## 5. Reorder List (LeetCode 143)

### Đề bài chi tiết
Cho `head` là danh sách $L_0 \rightarrow L_1 \rightarrow \dots \rightarrow L_{n-1} \rightarrow L_n$. Chắp nối (Reorder) lại thành $L_0 \rightarrow L_n \rightarrow L_1 \rightarrow L_{n-1} \rightarrow \dots$. Yêu cầu không đổi giá trị node mà phải thay đổi cấu trúc liên kết.

### Phân tích thuật toán
Kết hợp 3 bài toán:
1. Tìm node giữa bằng Fast/Slow.
2. Cắt đôi list, đảo ngược nửa thứ hai.
3. Ghép xen kẽ (Merge/Interleave) nửa đầu và nửa sau đã đảo ngược.

### Mã nguồn Java
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
        
        // 2. Tách list và đảo ngược nửa sau
        ListNode secondHalf = reverse(slow.next);
        slow.next = null; // Cắt đôi list
        
        // 3. Merge xen kẽ
        ListNode firstHalf = head;
        while (secondHalf != null) {
            ListNode temp1 = firstHalf.next;
            ListNode temp2 = secondHalf.next;
            
            firstHalf.next = secondHalf;
            secondHalf.next = temp1;
            
            firstHalf = temp1;
            secondHalf = temp2;
        }
    }
    
    private ListNode reverse(ListNode head) {
        ListNode prev = null, curr = head;
        while (curr != null) {
            ListNode temp = curr.next;
            curr.next = prev;
            prev = curr;
            curr = temp;
        }
        return prev;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(1)$

---

## 6. Happy Number (LeetCode 202)

### Đề bài chi tiết
Xác định xem $n$ có phải số hạnh phúc hay không. Một số hạnh phúc là số bắt đầu bằng tổng bình phương các chữ số của nó, thay thế số đó, và lặp lại cho tới khi kết quả bằng 1, hoặc nó lặp thành chu trình (không bao giờ ra 1).

### Phân tích thuật toán
Có thể coi phép tính "tổng bình phương các chữ số" là thao tác tìm `node.next`. Khi đó, chuỗi các tổng chính là một Linked List. Ta dùng Fast/Slow: `slow` tính một lần, `fast` tính hai lần. Nếu `fast` gặp 1 thì là Happy Number. Nếu `slow == fast` thì là chu trình vòng lặp (không phải).

### Mã nguồn Java
```java
public class Solution {
    public boolean isHappy(int n) {
        int slow = n, fast = n;
        
        do {
            slow = getSquareSum(slow);          // 1 bước
            fast = getSquareSum(getSquareSum(fast)); // 2 bước
        } while (slow != fast);
        
        return slow == 1;
    }
    
    private int getSquareSum(int num) {
        int sum = 0;
        while (num > 0) {
            int digit = num % 10;
            sum += digit * digit;
            num /= 10;
        }
        return sum;
    }
}
```

### Độ phức tạp
- **Time Complexity:** Khó xác định cụ thể, $O(\log n)$ thao tác nội bộ.
- **Space Complexity:** $O(1)$

---

## 7. Find the Duplicate Number (LeetCode 287)

### Đề bài chi tiết
Cho mảng `nums` gồm $n + 1$ số nguyên giới hạn từ $1$ đến $n$. Chắc chắn tồn tại ít nhất 1 số bị lặp lại. Tìm số lặp lại đó mà không làm thay đổi mảng gốc và chỉ dùng Space $O(1)$.

### Phân tích thuật toán
Vì các số từ $1$ tới $n$, ta có thể coi giá trị mảng như con trỏ `next`: `node = nums[node]`. Do có phần tử trùng lặp, chắc chắn sẽ có hai chỉ số cùng trỏ đến một giá trị (nhiều Node chĩa về 1 Node), tạo ra chu trình. Bài toán biến thành bài "Tìm Node bắt đầu chu trình".

### Mã nguồn Java
```java
public class Solution {
    public int findDuplicate(int[] nums) {
        int slow = nums[0];
        int fast = nums[0];
        
        // 1. Tìm điểm giao nhau
        do {
            slow = nums[slow];
            fast = nums[nums[fast]];
        } while (slow != fast);
        
        // 2. Tìm điểm bắt đầu chu trình (giá trị bị lặp)
        slow = nums[0];
        while (slow != fast) {
            slow = nums[slow];
            fast = nums[fast];
        }
        
        return slow;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(1)$

---

## 8. Delete the Middle Node of a Linked List (LeetCode 2095)

### Đề bài chi tiết
Xoá phần tử nằm chính giữa của danh sách liên kết. Nếu có 2 điểm giữa (độ dài chẵn), xoá điểm thứ 2. Trả về head của danh sách đã chỉnh sửa.

### Phân tích thuật toán
Dùng Fast/Slow Pointer nhưng cần lưu lại `prev` của `slow` để thực hiện phép ngắt kết nối `prev.next = slow.next`. Tối ưu hơn: Cho `fast` xuất phát trễ hoặc khởi tạo khác để `slow` đứng ngay trước node giữa khi `fast` kết thúc.

### Mã nguồn Java
```java
public class Solution {
    public ListNode deleteMiddle(ListNode head) {
        // Base case: 0 hoặc 1 node
        if (head == null || head.next == null) return null;
        
        ListNode slow = head;
        ListNode fast = head.next.next; // fast đi trước 2 bước
        
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        // Lúc này slow đang đứng ngay TRƯỚC middle node
        slow.next = slow.next.next;
        
        return head;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(1)$

---

## 9. Maximum Twin Sum of a Linked List (LeetCode 2130)

### Đề bài chi tiết
Một danh sách độ dài chẵn $n$. "Twin" của node ở vị trí $i$ là node ở vị trí $(n - 1 - i)$. Tìm tổng lớn nhất giữa 2 twins trong toàn bộ danh sách.

### Phân tích thuật toán
1. Dùng Fast/Slow tìm phần nửa thứ hai.
2. Đảo ngược nửa thứ hai.
3. Duyệt đồng thời nửa đầu và nửa sau đã đảo ngược, tính tổng `node1.val + node2.val` và tìm giá trị lớn nhất.

### Mã nguồn Java
```java
public class Solution {
    public int pairSum(ListNode head) {
        ListNode slow = head, fast = head;
        
        // 1. Middle
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        // 2. Reverse second half
        ListNode prev = null;
        ListNode curr = slow;
        while (curr != null) {
            ListNode nextNode = curr.next;
            curr.next = prev;
            prev = curr;
            curr = nextNode;
        }
        
        // 3. Find max sum
        int maxVal = 0;
        ListNode firstHalf = head;
        ListNode secondHalf = prev; // Head of reversed
        
        while (secondHalf != null) {
            maxVal = Math.max(maxVal, firstHalf.val + secondHalf.val);
            firstHalf = firstHalf.next;
            secondHalf = secondHalf.next;
        }
        
        return maxVal;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(1)$

---

## 10. Circular Array Loop (LeetCode 457)

### Đề bài chi tiết
Có một mảng vòng tròn chứa các số nguyên. Nếu giá trị dương, tiến lên $k$ bước; nếu âm, lùi $k$ bước. Kiểm tra xem có chu trình có độ dài $> 1$ và tất cả các node trong chu trình đều chuyển động cùng một hướng (hoàn toàn dương hoặc hoàn toàn âm) không.

### Phân tích thuật toán
Sử dụng Fast/Slow pointer cho mỗi index chưa thăm. Cần phải kiểm tra sự đổi hướng: nếu đang đi dương mà gặp bước nhảy âm (hoặc ngược lại) thì chu trình đó không hợp lệ, phải break.

### Mã nguồn Java
```java
public class Solution {
    public boolean circularArrayLoop(int[] nums) {
        int n = nums.length;
        
        for (int i = 0; i < n; i++) {
            if (nums[i] == 0) continue;
            
            int slow = i, fast = i;
            boolean isForward = nums[i] > 0;
            
            while (true) {
                slow = getNext(nums, slow, isForward);
                fast = getNext(nums, fast, isForward);
                if (fast != -1) fast = getNext(nums, fast, isForward);
                
                if (slow == -1 || fast == -1) break;
                if (slow == fast) {
                    return true;
                }
            }
            
            // Mark thăm thất bại bằng 0
            slow = i;
            int val = nums[i];
            while (nums[slow] * val > 0) {
                int nextNode = getNextWithoutValidation(nums, slow);
                nums[slow] = 0;
                slow = nextNode;
            }
        }
        
        return false;
    }
    
    private int getNext(int[] nums, int i, boolean isForward) {
        boolean currDirection = nums[i] > 0;
        if (currDirection != isForward) return -1;
        
        int n = nums.length;
        int next = (i + nums[i]) % n;
        if (next < 0) next += n;
        
        if (next == i) return -1; // Chu trình độ dài 1 không hợp lệ
        return next;
    }
    
    private int getNextWithoutValidation(int[] nums, int i) {
        int n = nums.length;
        int next = (i + nums[i]) % n;
        if (next < 0) next += n;
        return next;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N)$ (Mỗi node thăm tối đa hằng số lần nhờ cơ chế mark 0).
- **Space Complexity:** $O(1)$

---

## 11. Remove Nth Node From End of List (LeetCode 19)

### Đề bài chi tiết
Cho đầu của một danh sách liên kết, xóa node thứ $n$ từ cuối danh sách và trả về đầu của danh sách đó.

### Phân tích thuật toán
Sử dụng 2 con trỏ `fast` và `slow`. Ban đầu cho `fast` đi trước $n$ bước. Sau đó, cho cả `fast` và `slow` đi cùng lúc, mỗi lần 1 bước, cho đến khi `fast` chạm đến cuối danh sách (tức là `fast.next == null`). Lúc này, `slow` sẽ nằm ngay trước node cần xóa. Lưu ý dùng thêm một node giả (`dummy`) để xử lý trường hợp cần xóa chính node `head`.

### Mã nguồn Java
```java
public class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode slow = dummy;
        ListNode fast = dummy;
        
        for (int i = 0; i <= n; i++) {
            fast = fast.next;
        }
        
        while (fast != null) {
            slow = slow.next;
            fast = fast.next;
        }
        
        slow.next = slow.next.next;
        return dummy.next;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(1)$

---

## 12. Swap Nodes in Pairs (LeetCode 24)

### Đề bài chi tiết
Cho một danh sách liên kết, hoán đổi từng cặp node liền kề và trả về đầu danh sách. Cần phải hoán đổi các node thực sự chứ không chỉ thay đổi giá trị trong danh sách.

### Phân tích thuật toán
Sử dụng một node `dummy` trỏ tới `head`. Duyệt qua danh sách với con trỏ `prev` (ban đầu trỏ tới `dummy`). Trong mỗi bước, xác định hai node cần hoán đổi là `first` và `second`. Thay đổi các con trỏ `next` để `prev` trỏ tới `second`, `first` trỏ tới phần còn lại của danh sách, và `second` trỏ tới `first`. Sau đó di chuyển `prev` tới `first` để chuẩn bị cho cặp tiếp theo.

### Mã nguồn Java
```java
public class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode prev = dummy;
        
        while (prev.next != null && prev.next.next != null) {
            ListNode first = prev.next;
            ListNode second = prev.next.next;
            
            // Lật ngược liên kết
            first.next = second.next;
            second.next = first;
            prev.next = second;
            
            // Di chuyển prev cho cặp tiếp theo
            prev = first;
        }
        
        return dummy.next;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(1)$

---

## 13. Rotate List (LeetCode 61)

### Đề bài chi tiết
Cho đầu của một danh sách liên kết `head`, xoay danh sách sang phải $k$ vị trí.

### Phân tích thuật toán
Trước tiên, duyệt danh sách để tìm chiều dài $L$ và nối node cuối với node đầu để tạo thành chu trình vòng (circular). Sau đó, vị trí cắt đứt chu trình sẽ là $L - (k \bmod L)$. Duyệt từ đầu vòng $L - (k \bmod L)$ bước để đến node ngay trước điểm cắt, ngắt liên kết `next` bằng `null` và cập nhật `head` mới.

### Mã nguồn Java
```java
public class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        if (head == null || head.next == null || k == 0) return head;
        
        ListNode curr = head;
        int len = 1;
        while (curr.next != null) {
            curr = curr.next;
            len++;
        }
        
        curr.next = head; // Tạo chu trình vòng
        
        k = k % len;
        int stepsToNewHead = len - k;
        
        curr = head;
        for (int i = 0; i < stepsToNewHead - 1; i++) {
            curr = curr.next;
        }
        
        ListNode newHead = curr.next;
        curr.next = null; // Cắt vòng
        
        return newHead;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(1)$

---

## 14. Partition List (LeetCode 86)

### Đề bài chi tiết
Cho đầu danh sách liên kết `head` và một giá trị `x`, phân vùng danh sách sao cho tất cả các node có giá trị nhỏ hơn `x` đứng trước tất cả các node có giá trị lớn hơn hoặc bằng `x`. Phải giữ nguyên thứ tự ban đầu của các node trong cả hai phân vùng.

### Phân tích thuật toán
Tạo ra 2 danh sách liên kết giả (`dummy`): một cho các node nhỏ hơn `x` (gọi là `before`), một cho các node lớn hơn hoặc bằng `x` (gọi là `after`). Lặp qua danh sách gốc và thêm node vào `before` hoặc `after` tương ứng. Cuối cùng, nối đuôi của `before` với đầu của `after`, và đảm bảo đuôi của `after` trỏ đến `null`.

### Mã nguồn Java
```java
public class Solution {
    public ListNode partition(ListNode head, int x) {
        ListNode beforeDummy = new ListNode(0);
        ListNode afterDummy = new ListNode(0);
        ListNode before = beforeDummy;
        ListNode after = afterDummy;
        
        ListNode curr = head;
        while (curr != null) {
            if (curr.val < x) {
                before.next = curr;
                before = before.next;
            } else {
                after.next = curr;
                after = after.next;
            }
            curr = curr.next;
        }
        
        after.next = null;
        before.next = afterDummy.next;
        
        return beforeDummy.next;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(1)$

---

## 15. Sort List (LeetCode 148)

### Đề bài chi tiết
Cho đầu danh sách liên kết `head`, sắp xếp danh sách theo thứ tự tăng dần và trả về đầu danh sách. Yêu cầu độ phức tạp thời gian là $O(N \log N)$ và độ phức tạp bộ nhớ là $O(1)$ (nếu bỏ qua bộ nhớ của call stack trong đệ quy).

### Phân tích thuật toán
Sử dụng thuật toán Merge Sort chia để trị (Divide and Conquer). 
1. Sử dụng kỹ thuật Fast/Slow Pointer để tìm node chính giữa (Middle Node), từ đó chia danh sách làm hai nửa.
2. Đệ quy sắp xếp từng nửa.
3. Hợp nhất (Merge) hai nửa đã sắp xếp lại với nhau.

### Mã nguồn Java
```java
public class Solution {
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) return head;
        
        // 1. Chia đôi danh sách
        ListNode mid = getMid(head);
        ListNode left = head;
        ListNode right = mid.next;
        mid.next = null; // Cắt đôi
        
        // 2. Đệ quy sắp xếp
        left = sortList(left);
        right = sortList(right);
        
        // 3. Trộn
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
        ListNode tail = dummy;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                tail.next = l1;
                l1 = l1.next;
            } else {
                tail.next = l2;
                l2 = l2.next;
            }
            tail = tail.next;
        }
        tail.next = (l1 != null) ? l1 : l2;
        return dummy.next;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log N)$
- **Space Complexity:** $O(\log N)$ (do đệ quy).

---

## 16. Linked List Components (LeetCode 817)

### Đề bài chi tiết
Cho `head` của một danh sách liên kết chứa các số nguyên duy nhất và một mảng `nums` là tập con các giá trị có trong danh sách. Trả về số lượng các thành phần liên thông (connected components) trong `nums`. Một thành phần liên thông là một đoạn các node liên tiếp trong danh sách đều xuất hiện trong `nums`.

### Phân tích thuật toán
Dùng một HashSet để tra cứu nhanh các phần tử của mảng `nums`. Duyệt dọc theo danh sách liên kết: nếu node hiện tại có trong `nums` và node tiếp theo (nếu có) không có trong `nums` (hoặc node hiện tại là node cuối cùng), ta đếm đó là kết thúc của một thành phần liên thông, tăng biến đếm lên 1.

### Mã nguồn Java
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
            if (set.contains(curr.val) && (curr.next == null || !set.contains(curr.next.val))) {
                count++;
            }
            curr = curr.next;
        }
        
        return count;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N + M)$ với $M$ là độ dài mảng `nums`.
- **Space Complexity:** $O(M)$

---

## 17. Split Linked List in Parts (LeetCode 725)

### Đề bài chi tiết
Cho đầu danh sách liên kết `head` và một số nguyên `k`. Chia danh sách thành `k` phần liên tiếp. Độ dài của các phần nên chênh lệch tối đa 1 node. Các phần có độ dài lớn hơn sẽ nằm ở phía trước. Trả về mảng chứa đầu của `k` phần.

### Phân tích thuật toán
Trước tiên, đếm tổng số node $N$. Kích thước cơ bản của mỗi phần là $N / k$, và $N \pmod k$ phần đầu tiên sẽ có thêm 1 node (tức là có kích thước $N / k + 1$). Khởi tạo mảng kết quả, sau đó duyệt danh sách, cắt đứt các đoạn theo kích thước đã tính toán bằng cách thiết lập `prev.next = null`.

### Mã nguồn Java
```java
public class Solution {
    public ListNode[] splitListToParts(ListNode head, int k) {
        int n = 0;
        ListNode curr = head;
        while (curr != null) {
            n++;
            curr = curr.next;
        }
        
        int partSize = n / k;
        int extraNodes = n % k;
        
        ListNode[] res = new ListNode[k];
        curr = head;
        
        for (int i = 0; i < k; i++) {
            res[i] = curr;
            int currentPartSize = partSize + (extraNodes > 0 ? 1 : 0);
            extraNodes--;
            
            for (int j = 0; j < currentPartSize - 1; j++) {
                if (curr != null) {
                    curr = curr.next;
                }
            }
            
            if (curr != null) {
                ListNode nextPart = curr.next;
                curr.next = null;
                curr = nextPart;
            }
        }
        
        return res;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N + k)$
- **Space Complexity:** $O(k)$ (cho mảng kết quả).

---

## 18. Next Greater Node In Linked List (LeetCode 1019)

### Đề bài chi tiết
Cho `head` của một danh sách liên kết. Với mỗi node, tìm giá trị của node lớn hơn đầu tiên đứng sau nó. Trả về mảng các giá trị tương ứng (nếu không có thì trả về 0).

### Phân tích thuật toán
Ta có thể chuyển danh sách liên kết thành mảng để dễ dàng truy cập chỉ số (index). Sau đó, dùng kỹ thuật Monotonic Stack (Ngăn xếp đơn điệu). Stack lưu trữ chỉ số của các phần tử chưa tìm được giá trị lớn hơn tiếp theo. Khi gặp một phần tử lớn hơn đỉnh stack, lấy đỉnh stack ra và cập nhật giá trị kết quả cho index đó.

### Mã nguồn Java
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
        Stack<Integer> stack = new Stack<>();
        
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

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(N)$ (cho danh sách và ngăn xếp).

---

## 19. Odd Even Linked List (LeetCode 328)

### Đề bài chi tiết
Cho đầu danh sách liên kết `head`, nhóm tất cả các node ở vị trí lẻ (odd) lại với nhau, theo sau là tất cả các node ở vị trí chẵn (even). Lưu ý: đây là vị trí (index) lẻ/chẵn, không phải giá trị của node. Phải hoàn thành với độ phức tạp không gian $O(1)$.

### Phân tích thuật toán
Sử dụng hai con trỏ `odd` trỏ vào node thứ 1, và `even` trỏ vào node thứ 2. Giữ lại `evenHead` để sau đó có thể nối phần chẵn vào đuôi phần lẻ. Dùng vòng lặp cập nhật `odd.next = even.next` và `even.next = odd.next.next`, tịnh tiến cả hai cho đến khi hết danh sách. Cuối cùng, nối `odd.next = evenHead`.

### Mã nguồn Java
```java
public class Solution {
    public ListNode oddEvenList(ListNode head) {
        if (head == null || head.next == null) return head;
        
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

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(1)$

---

## 20. Swapping Nodes in a Linked List (LeetCode 1721)

### Đề bài chi tiết
Cho đầu danh sách liên kết `head` và một số nguyên `k`. Hoán đổi giá trị của node thứ `k` tính từ đầu và node thứ `k` tính từ cuối (với chỉ số bắt đầu từ 1) và trả về `head`.

### Phân tích thuật toán
Có thể thực hiện bằng cách hoán đổi giá trị. Duyệt con trỏ `fast` đi $k-1$ bước để tìm ra node thứ $k$ từ đầu, gọi là `first`. Sau đó dùng `slow` xuất phát từ `head` chạy cùng tốc độ với `fast` (từ `first`) cho tới khi `fast.next == null`. Lúc này `slow` sẽ là node thứ $k$ từ cuối, gọi là `second`. Cuối cùng, hoán đổi `first.val` và `second.val`.

### Mã nguồn Java
```java
public class Solution {
    public ListNode swapNodes(ListNode head, int k) {
        ListNode fast = head;
        // Đi tới node thứ k từ đầu (1-indexed)
        for (int i = 1; i < k; i++) {
            fast = fast.next;
        }
        
        ListNode first = fast;
        ListNode slow = head;
        
        // Đi tới node thứ k từ cuối
        while (fast.next != null) {
            slow = slow.next;
            fast = fast.next;
        }
        
        ListNode second = slow;
        
        // Hoán đổi giá trị
        int temp = first.val;
        first.val = second.val;
        second.val = temp;
        
        return head;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(1)$

---

## 21. Merge In Between Linked Lists (LeetCode 1669)

### Đề bài chi tiết
Cho hai danh sách liên kết `list1` và `list2`, cùng với 2 số nguyên `a` và `b`. Xoá các node từ vị trí `a` đến vị trí `b` (tính từ 0) của `list1`, sau đó chèn `list2` vào vị trí đó.

### Phân tích thuật toán
Dùng con trỏ duyệt danh sách `list1` để tìm node tại vị trí `a - 1` (gọi là `start`) và node tại vị trí `b + 1` (gọi là `end`). Sau đó, nối `start.next` với `list2`, duyệt tới cuối `list2` và nối đuôi của nó với `end`.

### Mã nguồn Java
```java
public class Solution {
    public ListNode mergeInBetween(ListNode list1, int a, int b, ListNode list2) {
        ListNode start = null;
        ListNode end = list1;
        
        for (int i = 0; i <= b; i++) {
            if (i == a - 1) {
                start = end;
            }
            end = end.next;
        }
        
        start.next = list2;
        while (list2.next != null) {
            list2 = list2.next;
        }
        
        list2.next = end;
        return list1;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N + M)$
- **Space Complexity:** $O(1)$

---

## 22. Reverse Nodes in k-Group (LeetCode 25)

### Đề bài chi tiết
Cho danh sách liên kết `head`, đảo ngược các node theo từng nhóm `k` phần tử và trả về danh sách đã được sửa đổi. Nếu số node không phải là bội số của `k` thì giữ nguyên các node cuối cùng đó.

### Phân tích thuật toán
Sử dụng con trỏ để đếm xem còn đủ `k` node hay không. Nếu đủ `k` node, gọi đệ quy hoặc dùng vòng lặp để đảo ngược `k` node đó. Sau đó kết nối đuôi của nhóm vừa đảo ngược với phần danh sách phía sau. Lặp lại quá trình trên.

### Mã nguồn Java
```java
public class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode curr = head;
        int count = 0;
        
        // Tìm node thứ k+1
        while (curr != null && count != k) {
            curr = curr.next;
            count++;
        }
        
        if (count == k) { 
            // Đảo ngược đoạn k node hiện tại
            curr = reverseKGroup(curr, k); 
            
            while (count-- > 0) { 
                ListNode tmp = head.next;
                head.next = curr;
                curr = head;
                head = tmp;
            }
            head = curr;
        }
        return head;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(N/k)$ (do đệ quy).

---

## 23. Copy List with Random Pointer (LeetCode 138)

### Đề bài chi tiết
Cho một danh sách liên kết có một con trỏ thêm `random` có thể trỏ tới bất kỳ node nào hoặc `null`. Hãy sao chép sâu (deep copy) danh sách này và trả về đầu của danh sách mới. Yêu cầu Space $O(1)$ không tính danh sách kết quả (không dùng HashMap).

### Phân tích thuật toán
1. Duyệt danh sách, với mỗi node tạo một node bản sao và chèn nó ngay sau node gốc. `A -> A' -> B -> B'`.
2. Duyệt lại, sao chép con trỏ `random`: `A'.random = A.random.next`.
3. Tách danh sách kết hợp thành 2 danh sách riêng biệt, trả về danh sách bản sao.

### Mã nguồn Java
```java
public class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) return null;
        
        // 1. Sao chép và đan xen node
        Node curr = head;
        while (curr != null) {
            Node copy = new Node(curr.val);
            copy.next = curr.next;
            curr.next = copy;
            curr = copy.next;
        }
        
        // 2. Cập nhật con trỏ random
        curr = head;
        while (curr != null) {
            if (curr.random != null) {
                curr.next.random = curr.random.next;
            }
            curr = curr.next.next;
        }
        
        // 3. Tách hai danh sách
        curr = head;
        Node pseudoHead = new Node(0);
        Node copyCurr = pseudoHead;
        
        while (curr != null) {
            Node nextReal = curr.next.next;
            copyCurr.next = curr.next;
            curr.next = nextReal;
            
            copyCurr = copyCurr.next;
            curr = nextReal;
        }
        
        return pseudoHead.next;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(1)$

---

## 24. Design Linked List (LeetCode 707)

### Đề bài chi tiết
Thiết kế một cấu trúc dữ liệu danh sách liên kết đơn hoặc đôi. Có các hàm: `get(index)`, `addAtHead(val)`, `addAtTail(val)`, `addAtIndex(index, val)`, `deleteAtIndex(index)`.

### Phân tích thuật toán
Để dễ triển khai, ta sử dụng một danh sách liên kết đơn (hoặc đôi) với một `dummy` node ở đầu và biến `size` để quản lý số lượng node. Các thao tác đều duyệt từ đầu (đối với danh sách đơn) đến vị trí cần thiết rồi sửa đổi các con trỏ `next`.

### Mã nguồn Java
```java
class MyLinkedList {
    class Node {
        int val;
        Node next;
        public Node(int val) { this.val = val; }
    }
    
    private Node head;
    private int size;

    public MyLinkedList() {
        head = new Node(0); // pseudo head
        size = 0;
    }
    
    public int get(int index) {
        if (index < 0 || index >= size) return -1;
        Node curr = head;
        for (int i = 0; i <= index; i++) {
            curr = curr.next;
        }
        return curr.val;
    }
    
    public void addAtHead(int val) {
        addAtIndex(0, val);
    }
    
    public void addAtTail(int val) {
        addAtIndex(size, val);
    }
    
    public void addAtIndex(int index, int val) {
        if (index > size) return;
        if (index < 0) index = 0;
        
        Node curr = head;
        for (int i = 0; i < index; i++) {
            curr = curr.next;
        }
        
        Node newNode = new Node(val);
        newNode.next = curr.next;
        curr.next = newNode;
        size++;
    }
    
    public void deleteAtIndex(int index) {
        if (index < 0 || index >= size) return;
        
        Node curr = head;
        for (int i = 0; i < index; i++) {
            curr = curr.next;
        }
        
        curr.next = curr.next.next;
        size--;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N)$ cho các thao tác get/add/delete theo index, $O(1)$ cho addAtHead.
- **Space Complexity:** $O(1)$ cho mỗi thao tác, tổng không gian $O(N)$.

---

## 25. Insertion Sort List (LeetCode 147)

### Đề bài chi tiết
Sắp xếp một danh sách liên kết sử dụng thuật toán sắp xếp chèn (Insertion Sort).

### Phân tích thuật toán
Dùng một `dummy` node để trỏ tới đầu danh sách đã được sắp xếp. Lặp qua danh sách ban đầu, với mỗi node, ta tìm vị trí thích hợp trong danh sách đã sắp xếp (bắt đầu duyệt từ `dummy`) rồi chèn node đó vào.

### Mã nguồn Java
```java
public class Solution {
    public ListNode insertionSortList(ListNode head) {
        if (head == null) return null;
        
        ListNode dummy = new ListNode(0);
        ListNode curr = head;
        
        while (curr != null) {
            ListNode prev = dummy;
            // Tìm vị trí chèn
            while (prev.next != null && prev.next.val < curr.val) {
                prev = prev.next;
            }
            
            ListNode nextTemp = curr.next;
            // Chèn curr vào giữa prev và prev.next
            curr.next = prev.next;
            prev.next = curr;
            // Di chuyển sang node tiếp theo của danh sách gốc
            curr = nextTemp;
        }
        
        return dummy.next;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N^2)$
- **Space Complexity:** $O(1)$

---

## 26. Add Two Numbers II (LeetCode 445)

### Đề bài chi tiết
Cho hai danh sách liên kết đại diện cho hai số nguyên không âm. Chữ số có trọng số lớn nhất đứng ở đầu danh sách. Cộng hai số đó và trả về kết quả dưới dạng danh sách liên kết. Yêu cầu không chỉnh sửa (đảo ngược) danh sách đầu vào nếu có thể, hoặc dùng thuật toán đảo ngược trực tiếp.

### Phân tích thuật toán
Có thể đảo ngược hai danh sách liên kết, rồi thực hiện phép cộng như bình thường (bài Add Two Numbers I). Kết quả tạo ra cũng được nối vào đầu (hoặc đảo ngược lại) để đảm bảo đúng thứ tự. 

### Mã nguồn Java
```java
public class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        l1 = reverseList(l1);
        l2 = reverseList(l2);
        
        ListNode head = null;
        int carry = 0;
        
        while (l1 != null || l2 != null || carry > 0) {
            int sum = carry;
            if (l1 != null) {
                sum += l1.val;
                l1 = l1.next;
            }
            if (l2 != null) {
                sum += l2.val;
                l2 = l2.next;
            }
            
            ListNode node = new ListNode(sum % 10);
            node.next = head; // Chèn vào đầu để kết quả theo đúng thứ tự
            head = node;
            carry = sum / 10;
        }
        
        return head;
    }
    
    private ListNode reverseList(ListNode head) {
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

### Độ phức tạp
- **Time Complexity:** $O(M + N)$
- **Space Complexity:** $O(1)$ (nếu không tính danh sách kết quả).

---

## 27. Flatten a Multilevel Doubly Linked List (LeetCode 430)

### Đề bài chi tiết
Một danh sách liên kết đôi đa cấp chứa các con trỏ `next`, `prev` và `child`. Hãy làm phẳng (flatten) nó để trả về một danh sách liên kết đôi bình thường. Tất cả các node từ danh sách con sẽ xuất hiện giữa node cha và node tiếp theo của cha.

### Phân tích thuật toán
Duyệt qua danh sách, khi gặp một node có `child`, hãy tìm node cuối cùng (tail) của chuỗi `child` đó. Nối `tail` với `curr.next`, và nối `curr` với `child`. Cập nhật các con trỏ `prev` tương ứng và gán `curr.child = null`. Sau đó tiếp tục duyệt.

### Mã nguồn Java
```java
public class Solution {
    public Node flatten(Node head) {
        if (head == null) return null;
        Node curr = head;
        
        while (curr != null) {
            if (curr.child != null) {
                Node nextNode = curr.next;
                Node child = curr.child;
                
                curr.next = child;
                child.prev = curr;
                curr.child = null;
                
                Node tail = child;
                while (tail.next != null) {
                    tail = tail.next;
                }
                
                tail.next = nextNode;
                if (nextNode != null) {
                    nextNode.prev = tail;
                }
            }
            curr = curr.next;
        }
        
        return head;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(1)$

---

## 28. Convert Sorted List to Binary Search Tree (LeetCode 109)

### Đề bài chi tiết
Cho đầu của một danh sách liên kết đơn đã được sắp xếp tăng dần, chuyển đổi nó thành một cây tìm kiếm nhị phân (BST) cân bằng chiều cao.

### Phân tích thuật toán
Cây cân bằng chiều cao được tạo bằng cách chọn phần tử ở giữa danh sách làm node gốc (Root). Sử dụng mẫu Fast/Slow Pointer để tìm phần tử giữa. Sau đó ngắt đôi danh sách tại điểm giữa và đệ quy tìm gốc cho nhánh trái và nhánh phải.

### Mã nguồn Java
```java
public class Solution {
    public TreeNode sortedListToBST(ListNode head) {
        if (head == null) return null;
        if (head.next == null) return new TreeNode(head.val);
        
        ListNode prev = null;
        ListNode slow = head;
        ListNode fast = head;
        
        // Tìm middle node
        while (fast != null && fast.next != null) {
            prev = slow;
            slow = slow.next;
            fast = fast.next.next;
        }
        
        // Ngắt đôi list
        if (prev != null) {
            prev.next = null;
        }
        
        TreeNode root = new TreeNode(slow.val);
        root.left = sortedListToBST(head);
        root.right = sortedListToBST(slow.next);
        
        return root;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(N \log N)$
- **Space Complexity:** $O(\log N)$ (do đệ quy).

---

## 29. Design Browser History (LeetCode 1472)

### Đề bài chi tiết
Thiết kế cấu trúc dữ liệu mô phỏng lịch sử trình duyệt với các hàm `visit(url)`, `back(steps)`, `forward(steps)`.

### Phân tích thuật toán
Sử dụng một danh sách liên kết đôi để lưu trữ các URL. Có một con trỏ `curr` chỉ vào trang hiện tại. Khi `visit`, ngắt bỏ toàn bộ các trang phía trước (chỉ gán `curr.next = newNode`). Khi `back` hoặc `forward`, chỉ cần di chuyển con trỏ `curr` lùi hoặc tới tối đa `steps` bước (hoặc cho đến khi gặp `null`).

### Mã nguồn Java
```java
class BrowserHistory {
    class Node {
        String url;
        Node prev, next;
        public Node(String url) { this.url = url; }
    }
    
    private Node curr;

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

### Độ phức tạp
- **Time Complexity:** $O(1)$ cho `visit`, $O(steps)$ cho `back` và `forward`.
- **Space Complexity:** $O(N)$ với $N$ là tổng số lượng URL đã duyệt.

---

## 30. LRU Cache (LeetCode 146)

### Đề bài chi tiết
Thiết kế LRU Cache (bộ đệm ít sử dụng gần đây nhất) với các hàm `get(key)` và `put(key, value)` trong độ phức tạp thời gian $O(1)$.

### Phân tích thuật toán
Sử dụng kết hợp `HashMap` (truy xuất $O(1)$) và Danh sách liên kết đôi (Doubly Linked List). Danh sách liên kết đôi hỗ trợ việc thêm và xoá node trong $O(1)$ thời gian. Khi truy cập (get/put), ta đưa node đó lên đầu danh sách. Khi đầy sức chứa (`capacity`), ta loại bỏ node ở cuối danh sách (LRU).

### Mã nguồn Java
```java
import java.util.HashMap;
import java.util.Map;

class LRUCache {
    class Node {
        int key, value;
        Node prev, next;
        public Node(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }
    
    private int capacity;
    private Map<Integer, Node> map;
    private Node head, tail; // Dummy nodes

    public LRUCache(int capacity) {
        this.capacity = capacity;
        map = new HashMap<>();
        head = new Node(0, 0);
        tail = new Node(0, 0);
        head.next = tail;
        tail.prev = head;
    }
    
    public int get(int key) {
        if (map.containsKey(key)) {
            Node node = map.get(key);
            remove(node);
            insert(node); // Move to front (most recently used)
            return node.value;
        }
        return -1;
    }
    
    public void put(int key, int value) {
        if (map.containsKey(key)) {
            remove(map.get(key));
        }
        if (map.size() == capacity) {
            remove(tail.prev); // Remove LRU
        }
        insert(new Node(key, value));
    }
    
    private void remove(Node node) {
        map.remove(node.key);
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }
    
    private void insert(Node node) {
        map.put(node.key, node);
        node.next = head.next;
        node.prev = head;
        head.next.prev = node;
        head.next = node;
    }
}
```

### Độ phức tạp
- **Time Complexity:** $O(1)$ cho cả `get` và `put`.
- **Space Complexity:** $O(C)$ với $C$ là `capacity`.
