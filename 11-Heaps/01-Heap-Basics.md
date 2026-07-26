# 01. Heap Basics (Cơ bản về Heap)

## 1. Giới thiệu (Introduction)
Heap là một trong những Data Structure quan trọng nhất trong khoa học máy tính, đóng vai trò nền tảng cho nhiều thuật toán nổi tiếng như Heap Sort và các bài toán tìm kiếm phần tử lớn nhất/nhỏ nhất một cách hiệu quả.

## 2. Định nghĩa (Definition)
Heap là một Complete Binary Tree (Cây nhị phân hoàn chỉnh) thỏa mãn thuộc tính Heap (Heap property). Complete Binary Tree là cây mà ở mọi level, ngoại trừ level cuối cùng, đều chứa đầy đủ các node, và ở level cuối cùng, các node được điền từ trái sang phải.

## 3. Phân loại (Classification)
Có hai loại Heap phổ biến:
1. **Min Heap**: Giá trị của node cha (parent) luôn nhỏ hơn hoặc bằng giá trị của các node con (children). Do đó, node gốc (root) luôn là phần tử nhỏ nhất.
2. **Max Heap**: Giá trị của node cha luôn lớn hơn hoặc bằng giá trị của các node con. Node gốc luôn là phần tử lớn nhất.

## 4. Ứng dụng thực tế (Real-world Applications)
- **Priority Queue**: Hàng đợi ưu tiên trong hệ điều hành để quản lý các tiến trình (scheduling).
- **Thuật toán đồ thị**: Thuật toán Dijkstra tìm đường đi ngắn nhất, thuật toán Prim tìm cây khung nhỏ nhất (Minimum Spanning Tree).
- **Tìm Kth Largest/Smallest element**: Tìm phần tử lớn/nhỏ thứ K trong luồng dữ liệu (data stream).

## 5. Cách biểu diễn (Array Representation)
Một Complete Binary Tree có thể được biểu diễn một cách tự nhiên bằng một mảng (Array) 1 chiều. Giả sử node hiện tại ở chỉ số `i` (0-indexed):
- **Parent**: `(i - 1) / 2`
- **Left Child**: `2 * i + 1`
- **Right Child**: `2 * i + 2`

Nhờ tính chất hoàn chỉnh, biểu diễn bằng mảng giúp tiết kiệm bộ nhớ do không cần lưu pointer tới left/right child, đồng thời tối ưu Cache Locality.

## 6. Các thao tác cơ bản (Basic Operations)
- **Insert**: Thêm một phần tử vào Heap ở vị trí cuối cùng của mảng, sau đó thực hiện quá trình Sift-up để khôi phục Heap property. Độ phức tạp $O(\log N)$.
- **Extract (Min/Max)**: Xóa và trả về phần tử gốc (lớn nhất trong Max Heap hoặc nhỏ nhất trong Min Heap). Đưa phần tử cuối cùng của mảng lên làm gốc, sau đó thực hiện Sift-down. Độ phức tạp $O(\log N)$.
- **Peek**: Lấy giá trị của root mà không xóa. $O(1)$.

## 7. Các thao tác nâng cao (Advanced Operations)
### Sift-up (Swim/Bubble-up)
Khi một node có giá trị lớn hơn (trong Max Heap) hoặc nhỏ hơn (trong Min Heap) node cha của nó, ta cần đẩy nó lên trên:
1. So sánh node hiện tại với cha của nó.
2. Nếu vi phạm Heap property, hoán đổi hai node.
3. Lặp lại quá trình với node cha cho tới khi đến root hoặc thỏa mãn Heap property.

### Sift-down (Sink/Bubble-down)
Khi một node vi phạm Heap property với các con của nó, ta đẩy nó xuống dưới:
1. So sánh node hiện tại với các con của nó.
2. Tìm con lớn hơn (trong Max Heap) hoặc nhỏ hơn (trong Min Heap).
3. Nếu node hiện tại vi phạm thuộc tính với con đã chọn, hoán đổi chúng.
4. Lặp lại quá trình với node con mới cho tới khi trở thành leaf node hoặc thỏa mãn Heap property.

## 8. Thuật toán cốt lõi (Core Algorithms) - Heapify
**Heapify (Build Heap)**: Quá trình chuyển đổi một mảng ngẫu nhiên thành một Heap.
- Thay vì chèn từng phần tử bằng Insert (mất $O(N \log N)$), ta có thể bắt đầu từ các non-leaf nodes cuối cùng và gọi Sift-down.
- Node không phải leaf cuối cùng nằm ở vị trí `(N/2) - 1`. Ta duyệt lùi từ node này về `0` và thực hiện Sift-down cho mỗi node.

## 9. Chứng minh độ phức tạp (Proof of Time Complexity for Build Heap)
Build Heap mất $O(N)$ time complexity.
**Chứng minh**:
- Giả sử cây có độ cao $h$. Level dưới cùng (leaf) có $2^h$ nodes.
- Ở height $k$ tính từ dưới lên (leaf = 0, root = $h$), có khoảng $\frac{N}{2^{k+1}}$ nodes.
- Chi phí Sift-down một node ở height $k$ là tối đa $k$ bước hoán đổi.
- Tổng số phép tính $T = \sum_{k=0}^{h} k \times \frac{N}{2^{k+1}}$.
- Triển khai chuỗi toán học này dẫn đến $T = O(N)$.

## 10. Triển khai trong Java (Implementation with java.util.PriorityQueue)
Trong Java, `java.util.PriorityQueue` là một implementation của Min Heap (mặc định). Nó không thread-safe; nếu cần dùng multi-thread thì dùng `PriorityBlockingQueue`.

```java
import java.util.PriorityQueue;

public class HeapExample {
    public static void main(String[] args) {
        // Mặc định là Min Heap
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        minHeap.offer(10);
        minHeap.offer(5);
        minHeap.offer(20);
        
        System.out.println("Min: " + minHeap.peek()); // 5
        System.out.println("Extracted: " + minHeap.poll()); // 5
    }
}
```

## 11. Custom Comparators (Bộ so sánh tùy chỉnh)
Để tạo Max Heap hoặc so sánh Object, ta truyền Comparator.
```java
// Max Heap
PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> b - a);
// Hoặc
PriorityQueue<Integer> maxHeap2 = new PriorityQueue<>(Collections.reverseOrder());
```

## 12. So sánh với cấu trúc dữ liệu khác (Comparison)
- **Heap vs BST (Binary Search Tree)**: BST hỗ trợ tìm kiếm bất kỳ phần tử nào trong $O(\log N)$, còn Heap chỉ mạnh ở việc lấy Min/Max. Tìm phần tử bất kỳ trong Heap là $O(N)$. Build BST mất $O(N \log N)$ so với Build Heap mất $O(N)$.
- **Heap vs Array/List**: Array lấy Max/Min $O(N)$ (nếu unsorted) hoặc cần sắp xếp. Heap duy trì Min/Max sau mỗi Insert/Extract chỉ mất $O(\log N)$.

## 13. Độ phức tạp tính toán (Complexity Analysis)
- Build Heap: $O(N)$
- Peek: $O(1)$
- Insert (offer): $O(\log N)$
- Extract (poll): $O(\log N)$
- Remove (phần tử bất kỳ): $O(N)$ để tìm kiếm + $O(\log N)$ để sift = $O(N)$
- Space Complexity: $O(N)$ để chứa các phần tử.

## 14. Ưu điểm (Advantages)
- Tối ưu cực tốt cho bài toán cần liên tục lấy Min/Max.
- Cấu trúc bộ nhớ liền kề (Contiguous memory) dưới dạng Array, tối ưu cache.
- Build Tree cực nhanh với $O(N)$.

## 15. Nhược điểm (Disadvantages)
- Không hỗ trợ tra cứu (search) các phần tử ngẫu nhiên tốt.
- Không lấy được phần tử lớn nhất trong Min Heap nhanh chóng.
- Thứ tự các phần tử bằng nhau (duplicate elements) không được bảo toàn (not a stable data structure).

## 16. Các biến thể (Variants)
- D-ary Heap: Thay vì 2 con, mỗi node có D con. Giảm chiều sâu cây xuống $\log_D N$, Extract tốn thời gian hơn nhưng Insert nhanh hơn.
- Fibonacci Heap: Amortized $O(1)$ cho Insert, Decrease-Key. Dùng nhiều trong thuật toán Dijkstra nâng cao.
- Binomial Heap.

## 17. 20 Lỗi thường gặp (20 Common Bugs)
1. Dùng Min Heap khi bài toán yêu cầu Max Heap.
2. Tràn số trong Comparator của PriorityQueue (`a - b` gây overflow, nên dùng `Integer.compare(a, b)`).
3. Nhầm lẫn công thức parent, left, right khi mảng 1-indexed so với 0-indexed.
4. Quên cập nhật index khi hoán đổi trong Custom Heap có tính năng Decrease-Key.
5. Poll phần tử ra khỏi Heap nhưng chưa check Heap empty, gây `NullPointerException` hoặc `NoSuchElementException`.
6. Gọi phương thức `remove(Object)` của PriorityQueue, độ phức tạp $O(N)$, lầm tưởng nó là $O(\log N)$.
7. Khi custom object trong Queue thay đổi thuộc tính, không update Heap, làm hỏng Heap property.
8. Quên break loop khi Sift-up đã thỏa mãn điều kiện cha lớn/nhỏ hơn con.
9. Sift-down nhầm chỉ số của con lớn hơn thay vì con nhỏ hơn (hoặc ngược lại).
10. So sánh bằng tham chiếu `==` thay vì `equals()` đối với Custom Objects.
11. Đếm sai số lượng phần tử hiện có trong Heap khi tự code bằng mảng.
12. Truy cập mảng vượt ranh giới (ArrayIndexOutOfBounds) trong quá trình Sift-down.
13. Nhầm điều kiện lớn hơn/nhỏ hơn (`<` thay vì `>`) trong lúc code Min Heap thủ công.
14. Không clone mảng khởi tạo khiến Heap thay đổi trực tiếp mảng gốc (side effects).
15. Khởi tạo `PriorityQueue` với capacity 0 gây Exception.
16. Thêm `null` vào PriorityQueue, ném ra `NullPointerException`.
17. Custom Comparator trả về sai `0, 1, -1`, phá vỡ tính bắc cầu.
18. Không gọi Sift-down cho các non-leaf node khi Build Heap, chạy từ index `0` thay vì `N/2 - 1` ngược về 0.
19. Chạy Build Heap bằng cách gọi Insert vòng lặp ($O(N \log N)$ thay vì $O(N)$).
20. Cố gắng iterate trên PriorityQueue nghĩ rằng nó trả về thứ tự sorted. Iterator của PQ không đảm bảo trật tự lấy ra là Min/Max!

## 18. 30 Trường hợp biên (30 Edge Cases)
1. Heap rỗng (Empty Heap).
2. Heap chỉ chứa 1 phần tử.
3. Insert phần tử đã tồn tại (Duplicates).
4. Extract Min liên tục đến khi rỗng.
5. Sift-down khi node chỉ có duy nhất 1 con trái (không có con phải).
6. Tất cả phần tử trong mảng bằng nhau.
7. Mảng ban đầu đã được sắp xếp tăng dần (đối với Min Heap).
8. Mảng ban đầu đã được sắp xếp giảm dần (đối với Min Heap).
9. Mảng có độ dài là số chẵn.
10. Mảng có độ dài là số lẻ.
11. Max-capacity của Array Heap bị đầy, cần resize.
12. Resize mảng Heap vượt qua `Integer.MAX_VALUE`.
13. Lấy giá trị lớn nhất (MAX_INT) hoặc nhỏ nhất (MIN_INT) thêm vào Heap.
14. Truyền null comparator.
15. Build Heap từ mảng size 0.
16. Các giá trị âm lẫn lộn dương trong Heap.
17. Decrease/Increase key đến giá trị của chính nó.
18. Dữ liệu liên tục được offer và poll đồng thời.
19. Tìm kiếm phần tử không tồn tại trong Heap.
20. Heap có nhiều Duplicate Keys nằm liên tiếp trên cây.
21. Thêm một mảng rất lớn khiến OutOfMemoryError.
22. Khởi tạo Heap kích thước rất nhỏ nhưng Insert quá nhiều, gây tốn kém re-allocation liên tục.
23. Sử dụng Float/Double có giá trị `NaN` vào Heap.
24. Sử dụng Float/Double có `+0.0` và `-0.0`.
25. Mảng đang ở level cuối bị lấp đầy hoàn toàn.
26. Mảng chỉ thiếu 1 phần tử là đầy một Perfect Binary Tree.
27. Đẩy (push) 1 node rất nhỏ từ gốc xuống tận leaf node trong Sift-down.
28. Kéo (pull) 1 node rất nhỏ từ leaf lên tận gốc trong Sift-up.
29. Cây bị skew hoàn toàn bên trái/phải không thể xảy ra do tính chất Complete Tree.
30. Thay thế Root bằng giá trị mới thay vì poll rỗng rồi offer (thao tác `replace` - 최 ưu hóa).

## 19. 20 Câu hỏi phỏng vấn (20 Interview Questions)
1. Giải thích sự khác biệt giữa Min Heap và Max Heap?
2. Tại sao Heap lại được cài đặt bằng Array thay vì Pointer (Node-based)?
3. Phân tích độ phức tạp thời gian của thuật toán Build Heap (Heapify) và giải thích tại sao nó là $O(N)$?
4. Sift-up và Sift-down khác nhau thế nào và khi nào dùng chúng?
5. Làm thế nào để implement Priority Queue sử dụng Heap?
6. PriorityQueue trong Java là thread-safe không? Làm sao để có thread-safe Priority Queue?
7. Sự khác biệt giữa Binary Search Tree và Heap là gì? Khi nào nên dùng loại nào?
8. Kể tên một số ứng dụng thực tế của Heap.
9. Có thể thực hiện thao tác Search trên Heap trong bao lâu? Tại sao?
10. Phác thảo cách lấy $K$ phần tử lớn nhất trong luồng dữ liệu (Data Stream) sử dụng Heap. Tại sao không dùng Max Heap kích thước $N$ mà lại dùng Min Heap kích thước $K$?
11. Fibonacci Heap so với Binary Heap có điểm gì nổi bật?
12. Làm thế nào để lấy ra Iterator các phần tử của Java `PriorityQueue` theo đúng thứ tự ưu tiên? (Đảo lại: Iterator không đảm bảo thứ tự).
13. Giả sử ta muốn Merge 2 Heaps, cách nào làm nhanh nhất? ($O(M+N)$ thay vì lặp qua chèn từng phần tử).
14. Phân tích bài toán "Merge K Sorted Lists", làm sao Heap đóng vai trò trung tâm?
15. Khi thiết kế một task scheduler (lịch trình), Min Heap có thể lưu trữ thông tin gì?
16. Nếu một đối tượng đưa vào `PriorityQueue` thay đổi state sau đó, Heap có tự động được cập nhật không?
17. D-ary Heap hoạt động ra sao và tối ưu điểm gì so với Binary Heap?
18. Trình bày thuật toán Heap Sort.
19. Tại sao Heap Sort ít được sử dụng trong thực tế so với Quick Sort, dù Quick Sort có worst case là $O(N^2)$?
20. Bài toán: Tìm Median của một Data Stream. Làm sao dùng Heap để giải quyết?

## 20. Best Practices
- Sử dụng Custom Comparator bằng `Integer.compare` thay vì phép trừ để tránh overflow.
- Khi cần tìm Top $K$ elements lớn nhất, hãy dùng Min-Heap kích thước $K$. Khi cần tìm Top $K$ elements nhỏ nhất, dùng Max-Heap kích thước $K$.
- Để ý Heap property bị hỏng nếu object bên trong thay đổi giá trị thuộc tính tham gia so sánh. Cần remove rồi insert lại.

## 21. Cấu trúc bộ nhớ (Memory Layout)
Dữ liệu Heap lưu trong mảng. Các phần tử cha con thường nằm gần nhau ở những tầng đầu, nhưng càng xuống sâu, khoảng cách giữa cha và con trong mảng tăng theo cấp số nhân, có thể gây cache miss nhiều hơn so với một số cấu trúc dữ liệu thiết kế theo B-Tree. Tuy nhiên nó tốt hơn nhiều so với Linked Node.

## 22. Bài tập thực hành (Practice Problems)
Xem file `01-Heap-Basics-Problems.md` để áp dụng lý thuyết này vào thực tế giải quyết 30 bài toán cơ bản đến nâng cao.

## 23. Tài liệu tham khảo (References)
- CLRS (Introduction to Algorithms) - Chương về Heapsort.
- Tài liệu Java Docs: `java.util.PriorityQueue`.

## 24. Kết luận (Conclusion)
Heap là một cấu trúc dữ liệu đơn giản về mặt tổ chức (chỉ bằng một mảng) nhưng lại mang lại sức mạnh to lớn trong việc giải quyết các bài toán về độ ưu tiên. Nắm vững cách Build Heap, Sift-up, Sift-down cũng như ứng dụng Min/Max Heap với kích thước hằng số là hành trang quan trọng để chinh phục các vòng phỏng vấn kỹ thuật.
