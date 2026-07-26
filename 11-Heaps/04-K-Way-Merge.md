# 04. K-Way Merge Pattern

## 1. Giới thiệu (Introduction)
Mẫu **K-Way Merge** (kết hợp K đường) là một kỹ thuật mạnh mẽ giúp chúng ta giải quyết các bài toán liên quan đến nhiều danh sách hoặc mảng đã được sắp xếp (sorted arrays/lists). Kỹ thuật này thường sử dụng **Min-Heap** để tìm phần tử nhỏ nhất trong số tất cả các danh sách, sau đó chèn vào danh sách kết quả, và lặp lại quá trình này cho đến khi duyệt hết các phần tử.

## 2. Ứng dụng thực tế (Real-world applications)
- Hợp nhất các file log từ nhiều server khác nhau, trong đó mỗi file đã được sắp xếp theo thời gian (timestamp).
- **External sorting** (Sắp xếp ngoại vi): Khi dữ liệu quá lớn để đưa vào RAM, ta chia nhỏ, sắp xếp từng phần, và hợp nhất K mảng lại với nhau.
- Merge các luồng sự kiện (event streams) từ các nguồn phân tán.
- Các bài toán xử lý chuỗi DNA trong sinh học máy tính.

## 3. Phân tích trực giác (Intuition)
Nếu chỉ có 2 danh sách đã sắp xếp, ta dùng 2 con trỏ (Two Pointers) để hợp nhất trong thời gian O(N). Khi có K danh sách, ta cần K con trỏ. Tuy nhiên, việc tìm giá trị nhỏ nhất giữa K con trỏ tốn O(K) thời gian cho mỗi phần tử, tổng O(N*K).
Thay vào đó, ta có thể dùng **Min-Heap** chứa K phần tử (mỗi phần tử đại diện cho phần tử nhỏ nhất hiện tại của mỗi danh sách). Khi đó, lấy phần tử nhỏ nhất và thêm phần tử tiếp theo từ cùng danh sách vào Heap mất O(log K) thời gian.

## 4. Định nghĩa chi tiết (Detailed definition)
K-Way Merge giải quyết việc gom K tập hợp các phần tử (đã được sắp xếp) thành một tập hợp duy nhất cũng có thứ tự. Thuật toán duy trì một Heap (thường là Min-Heap) kích thước K. Mỗi node trong Heap chứa giá trị của phần tử, chỉ số của danh sách (array index) mà nó thuộc về, và chỉ số của phần tử trong danh sách đó (element index).

## 5. Cách hoạt động (How it works - step by step)
1. Khởi tạo một Min-Heap.
2. Lấy phần tử đầu tiên (nhỏ nhất) từ mỗi mảng/danh sách trong số K mảng/danh sách và đưa vào Min-Heap. Đi kèm với phần tử là `array_index` và `element_index`.
3. Trong khi Min-Heap không rỗng:
   - Pop phần tử nhỏ nhất ra khỏi Min-Heap. Đưa giá trị này vào mảng kết quả.
   - Dùng `array_index` để biết phần tử vừa pop thuộc về mảng nào, và `element_index` để biết phần tử tiếp theo của mảng đó.
   - Nếu mảng đó vẫn còn phần tử, push phần tử tiếp theo vào Min-Heap với `element_index + 1`.

## 6. Phân tích độ phức tạp (Complexity analysis)
- **Time Complexity**: Khởi tạo Heap với K phần tử tốn $O(K \log K)$ hoặc $O(K)$. Sau đó, với mỗi phần tử (tổng cộng N phần tử), ta thực hiện thao tác pop và push tốn $O(\log K)$. Tổng độ phức tạp là $O(N \log K)$.
- **Space Complexity**: Heap luôn lưu trữ tối đa K phần tử, do đó độ phức tạp bộ nhớ là $O(K)$. Mảng kết quả tốn $O(N)$. Tổng cộng là $O(K + N)$ (thường là $O(K)$ extra space).

## 7. Ưu điểm và nhược điểm (Pros and cons)
**Ưu điểm**:
- Giảm thiểu thời gian so sánh từ $O(K)$ xuống $O(\log K)$ cho mỗi phần tử.
- Rất hiệu quả khi số lượng mảng K lớn và các mảng phân tán.
- Chỉ cần bộ nhớ $O(K)$ (không tính mảng kết quả), phù hợp cho xử lý streaming.

**Nhược điểm**:
- Không hiệu quả nếu K quá nhỏ (ví dụ K=2, 2 con trỏ nhanh hơn do không có overhead của Heap).
- Thêm phức tạp trong việc triển khai do phải xây dựng class để theo dõi `array_index` và `element_index`.

## 8. Khi nào nên sử dụng (When to use)
- Có "K" danh sách (arrays, linked lists, matrices) đã sắp xếp và cần hợp nhất (merge) hoặc tìm phần tử thứ $k$.
- Bài toán liên quan đến "thứ tự" (sorted order) từ nhiều nguồn cấp dữ liệu khác nhau.
- Bài toán yêu cầu duyệt ma trận có các hàng và cột được sắp xếp.

## 9. So sánh với các cấu trúc dữ liệu/thuật toán khác (Comparison)
- **Divide and Conquer**: Có thể merge từng cặp mảng, cũng đạt $O(N \log K)$. Tuy nhiên, Min-Heap dễ triển khai hơn trong mô hình dữ liệu stream.
- **Two Pointers**: Chỉ dùng tốt cho $K=2$.
- **Sorting toàn bộ**: Trộn K mảng lại rối sort lại tốn $O(N \log N)$. K-Way Merge vượt trội với $O(N \log K)$.

## 10. Implement cơ bản (Basic implementation in Java)
```java
class Node implements Comparable<Node> {
    int val;
    int arrayIndex;
    int elementIndex;

    public Node(int val, int arrayIndex, int elementIndex) {
        this.val = val;
        this.arrayIndex = arrayIndex;
        this.elementIndex = elementIndex;
    }

    public int compareTo(Node other) {
        return Integer.compare(this.val, other.val);
    }
}
// Logic sử dụng PriorityQueue (Min-Heap)
```

## 11. Các biến thể phổ biến (Common variants)
- **Kth Smallest Element**: Thay vì merge toàn bộ, chỉ cần pop K lần từ Min-Heap.
- **Max-Heap thay vì Min-Heap**: Dùng khi cần tìm phần tử lớn nhất hoặc danh sách giảm dần.
- **Merge K streams**: Dữ liệu đến liên tục, sử dụng iterator thay cho chỉ số mảng.

## 12. Các kỹ thuật tối ưu (Optimization techniques)
- Nếu các mảng có độ dài chênh lệch lớn, Divide and Conquer có thể ít bị overhead hơn.
- Với ngôn ngữ không có Priority Queue tích hợp sẵn, sử dụng Balanced BST hoặc tự viết Heap array-based cho hiệu suất cache tốt hơn.

## 13. 30 Edge cases phổ biến (30 Common edge cases)
1. Các danh sách đầu vào rỗng hoàn toàn.
2. Một danh sách rỗng, các danh sách khác có phần tử.
3. K = 1 (chỉ có 1 danh sách).
4. Các phần tử trong danh sách đều giống nhau.
5. Danh sách chứa số âm, số 0.
6. Một danh sách rất dài, các danh sách khác rất ngắn.
7. Số lượng danh sách (K) lớn hơn tổng số phần tử.
8. `k` (để tìm K-th element) lớn hơn tổng số phần tử.
9. `k` = 1 (tìm giá trị min ngay lập tức).
10. `k` bằng đúng tổng số phần tử của tất cả các danh sách.
11. Các giá trị trong danh sách vượt quá kích thước Integer (tràn số, Integer.MAX_VALUE).
12. Integer.MIN_VALUE có mặt ở đầu tất cả các danh sách.
13. Danh sách đầu vào là danh sách liên kết thay vì mảng.
14. Các mảng có độ dài khác nhau hoàn toàn.
15. Tất cả K danh sách có phần tử đầu tiên bằng nhau.
16. Dữ liệu mảng chứa null elements (đối với danh sách liên kết).
17. Heap tự hết phần tử trước khi k bước hoàn tất.
18. Yêu cầu sắp xếp ngược (Max-Heap) thay vì Min-Heap.
19. Tổng kích thước các mảng N là rất nhỏ (K=3, N=3).
20. Mảng chỉ có 1 phần tử mỗi mảng.
21. Tràn bộ nhớ do N quá lớn (cần quản lý luồng dữ liệu - stream).
22. Các phần tử trùng lặp trong một danh sách và giữa các danh sách.
23. K danh sách được truyền dưới dạng mảng 2D thưa (sparse 2D array).
24. Sử dụng mảng bất biến (immutable arrays).
25. Iterator của luồng dữ liệu bị lỗi trong quá trình lấy giá trị (Exception handling).
26. Việc truy cập bộ nhớ cho `elementIndex + 1` ném ra IndexOutOfBounds.
27. Đồ thị ma trận chứa kích thước mxn nhưng m và n khác biệt lớn.
28. Một số cột trong ma trận không được sắp xếp đúng yêu cầu.
29. Cấu trúc custom Node chưa cài đặt logic equal()/hashCode().
30. Tìm K-th smallest in a matrix, k = ma trận kích thước 1x1.

## 14. 20 Lỗi thường gặp (20 Common bugs/pitfalls)
1. Quên thêm phần tử đầu tiên của mỗi danh sách vào Heap lúc khởi tạo.
2. Xử lý thiếu NullPointerException khi danh sách là null.
3. Heap push với mảng đã chạy hết phần tử (Out of bounds).
4. Khởi tạo K trong Heap lớn hơn số danh sách không rỗng (lỗi chỉ số).
5. Sử dụng hàm Compare bị ngược (Min-Heap thành Max-Heap).
6. Viết Class Node nhưng không implement Comparable dẫn đến ClassCastException.
7. Pop khỏi PriorityQueue mà không kiểm tra `.isEmpty()`.
8. Lầm lẫn giữa K danh sách và việc lấy phần tử thứ K.
9. Quên tăng `element_index` khi lấy phần tử tiếp theo từ cùng mảng.
10. Tràn số khi tính tổng K element bằng custom object.
11. Truy xuất danh sách liên kết bằng index thay vì gọi node.next (tốn $O(N^2)$).
12. Quản lý trạng thái Node sai khi phần tử tiếp theo là null.
13. Không loại trừ các mảng rỗng trong lúc khởi tạo ban đầu, thêm node bị lỗi.
14. Tưởng lầm độ phức tạp là $O(N \log N)$ thay vì $O(N \log K)$.
15. Không xử lý được các số âm lớn trong thuật toán compare (lỗi Overflow: a-b).
16. Khởi tạo PriorityQueue mặc định không ghi rõ dung lượng và comparator trong trường hợp dữ liệu phức tạp.
17. Không tính tới trường hợp mảng có chứa bản sao y hệt nhau.
18. Không tối ưu được Memory Limit Exceeded vì push toàn bộ K mảng vào Heap một lúc.
19. Sửa đổi trực tiếp dữ liệu gốc thay vì dùng clone hoặc tham chiếu an toàn.
20. Khởi tạo mảng kết quả kích thước sai dẫn đến ArrayIndexOutOfBoundsException.

## 15. 20 Câu hỏi phỏng vấn phổ biến (20 Common interview questions)
1. Làm thế nào để gộp K danh sách liên kết đã sắp xếp?
2. Hãy so sánh Divide and Conquer với Min-Heap trong bài toán gộp K danh sách.
3. Phân tích độ phức tạp thời gian khi dùng Max-Heap và Min-Heap?
4. Bài toán tìm K-th Smallest Element in a Matrix có thể giải bằng K-Way Merge không?
5. Nếu K rất lớn (K = 1,000,000) nhưng bộ nhớ trong rất bé, bạn xử lý ra sao?
6. Bạn viết hàm Compare() trong Java như thế nào để tránh lỗi tràn số khi trừ hai số?
7. Sự khác biệt giữa gộp 2 danh sách và gộp K danh sách là gì?
8. Tại sao độ phức tạp lại là $O(N \log K)$? N là gì và K là gì?
9. Bạn có thể sử dụng BST thay cho Min-Heap được không? Vì sao?
10. Smallest Range Covering Elements from K Lists có phải là ứng dụng của K-Way Merge?
11. Cách tiếp cận thuật toán tối ưu để hợp nhất các chuỗi logs theo thời gian?
12. Làm sao tìm được cặp K-th từ 2 mảng chưa sắp xếp hoàn toàn mà chỉ sắp xếp một phần?
13. Tìm phần tử nhỏ nhất thứ k trong $n^2$ phần tử (Bảng cửu chương ma trận).
14. Cấu trúc dữ liệu Node cần có bao nhiêu thuộc tính để theo dõi vị trí mảng và phần tử?
15. Xử lý thế nào nếu dữ liệu thay vì là mảng, lại được cung cấp thông qua Iterator?
16. Code tìm số "Ugly number" thứ K sử dụng Heap như thế nào?
17. Giải thích thuật toán để tìm "K Pairs with Smallest Sums" bằng Heap.
18. Khi nào thì dùng Binary Search on Answer tốt hơn K-Way Merge (ví dụ trong bài ma trận)?
19. Giải thích khái niệm "External Sort" trong hệ quản trị cơ sở dữ liệu (DBMS).
20. Hướng giải quyết nếu mảng 2D có các hàng được sắp xếp nhưng không có quy luật nào về cột?

## 16. Hướng dẫn tư duy giải quyết vấn đề (Problem-solving mindset)
Khi đề bài có những manh mối như: "K sorted arrays", "K sorted linked lists", "matrix with sorted rows/columns", "find kth smallest/largest"... thì phản xạ đầu tiên nên là K-Way Merge. 
Cách tư duy:
- Ta có thể duyệt tất cả không? Nếu duyệt thì tốn bao nhiêu.
- Liệu có thể duy trì một tập hợp "kích thước K" của các ứng viên sáng giá nhất (smallest/largest) bằng PriorityQueue không?
- Khi lấy 1 phần tử ra, ta thay thế nó bằng phần tử kề sau từ cùng một danh sách nguồn.

## 17. Các Design pattern liên quan (Related design patterns)
- **Iterator Pattern**: Rất phổ biến khi duyệt K stream hoặc file khổng lồ thay vì tải toàn bộ vào mảng. K-Way merge tiêu thụ phần tử qua Iterator của từng mảng.
- **Strategy Pattern**: Thay đổi Comparator (Min/Max Heap) một cách linh hoạt mà không thay đổi logic merge cốt lõi.

## 18. Câu hỏi tự kiểm tra (Self-assessment questions)
1. Bạn có biết tự implement PriorityQueue cho K-Way Merge bằng mảng không?
2. Kích thước Heap tối đa là bao nhiêu trong toàn bộ vòng đời chạy thuật toán?
3. Tại sao trong K-way merge với linked list ta đưa luôn object ListNode vào Heap thay vì tạo object Node mới?

## 19. Tài liệu tham khảo (References)
- "Introduction to Algorithms" by Cormen et al. (Chương về Heapsort và Data Structures).
- Các bài giảng về External Sort trên hệ thống cơ sở dữ liệu.

## 20. Cheat sheet
- **Dấu hiệu nhận biết**: Danh sách "đã sắp xếp", "K danh sách", "phần tử thứ K".
- **Cấu trúc dữ liệu chính**: `PriorityQueue<Node> pq = new PriorityQueue<>((a,b) -> a.val - b.val);`
- **Node attributes**: `int value`, `int arrayIndex`, `int elementIndex`. (Với Linked List chỉ cần `ListNode` với Comparator).
- **Time/Space**: Time $O(N \log K)$, Space $O(K)$.

## 21. Lời khuyên từ chuyên gia (Expert advice)
- Khi đi phỏng vấn bằng Java, hãy làm quen với cú pháp lambda `(a, b) -> Integer.compare(a.val, b.val)` để viết Comparator nhanh chóng thay vì Anonymous Inner Class dài dòng.
- Luôn kiểm tra `.isEmpty()` đối với các mảng ban đầu trước khi đẩy chúng vào Heap, nhiều ứng viên bị fail ở vòng phỏng vấn vì edge case này.

## 22. Thực hành (Practice)
Khuyến nghị thực hành bài "Merge k Sorted Lists", "Kth Smallest Element in a Sorted Matrix", "Find K Pairs with Smallest Sums" trên LeetCode để thành thạo khuôn mẫu này.

## 23. Kết luận (Conclusion)
K-Way Merge là một Design Pattern không thể thiếu trong cấu trúc dữ liệu thuật toán, giúp biến việc hợp nhất nhiều nguồn dữ liệu (từ mảng, file đến stream) trở nên cực kỳ hiệu quả về cả thời gian và không gian bộ nhớ.

## 24. Hướng dẫn học tiếp (Next steps)
- Nắm vững **Top K Elements Pattern**.
- Tìm hiểu Binary Search on Answer để so sánh khả năng với K-Way Merge trong bài toán ma trận.
- Tìm hiểu cách Hệ điều hành và Hệ quản trị CSDL làm External Sort.
