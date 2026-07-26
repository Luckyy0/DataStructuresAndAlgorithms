# 02 - Top K Elements Pattern

## 1. Giới thiệu (Introduction)
Pattern **Top K Elements** là một trong những kỹ thuật phổ biến nhất để giải quyết các bài toán yêu cầu tìm "K phần tử lớn nhất/nhỏ nhất/xuất hiện nhiều nhất" trong một tập hợp dữ liệu.

## 2. Định nghĩa (Definition)
Mẫu thuật toán này sử dụng cấu trúc dữ liệu **Heap** (thường là Min-Heap hoặc Max-Heap) có kích thước cố định $K$ để duy trì danh sách các phần tử thỏa mãn điều kiện "Top K" trong quá trình duyệt qua dữ liệu.

## 3. Mục đích (Purpose)
- Giảm thiểu Time Complexity từ $O(N \log N)$ (khi dùng Sorting) xuống còn $O(N \log K)$.
- Hỗ trợ xử lý dữ liệu luồng (stream) mà không cần lưu trữ toàn bộ dữ liệu.

## 4. Cách nhận diện (How to recognize)
- Đề bài yêu cầu tìm "K phần tử lớn nhất", "K phần tử nhỏ nhất", "K phần tử xuất hiện nhiều nhất", "K phần tử gần nhất".
- Từ khóa: "Top K", "Kth largest/smallest", "K most frequent".

## 5. Kiến thức yêu cầu (Prerequisites)
- Hiểu biết về Priority Queue / Heap.
- Nắm vững các thao tác: `push` (insert), `pop` (extract), `peek` (top).
- Kiến thức về Hash Map (khi kết hợp đếm tần suất).

## 6. Cách tiếp cận và giải thuật (Approach & Algorithm)
- **Để tìm Top K lớn nhất (Top K Largest):** Sử dụng **Min-Heap** có kích thước K. Khi Heap đầy, nếu phần tử đang xét lớn hơn đỉnh (root) của Min-Heap, ta xóa đỉnh và chèn phần tử mới vào.
- **Để tìm Top K nhỏ nhất (Top K Smallest):** Sử dụng **Max-Heap** có kích thước K. Nếu phần tử đang xét nhỏ hơn đỉnh của Max-Heap, ta xóa đỉnh và chèn phần tử mới vào.

## 7. Phân tích độ phức tạp (Complexity Analysis)
- **Time Complexity:** $O(N \log K)$ vì mỗi thao tác push/pop trên Heap kích thước K tốn $O(\log K)$, thực hiện $N$ lần.
- **Space Complexity:** $O(K)$ để lưu trữ Heap. Thêm $O(N)$ nếu cần Hash Map đếm tần suất.

## 8. So sánh với các kỹ thuật khác (Comparison with other techniques)
- **Sorting:** $O(N \log N)$ thời gian. Chậm hơn khi $K \ll N$.
- **Quickselect (Hoare's Selection):** $O(N)$ trung bình, $O(N^2)$ worst-case. Hiệu quả để tìm phần tử lớn thứ K, nhưng không duy trì được toàn bộ danh sách "Top K" có thứ tự một cách dễ dàng trong môi trường Streaming dữ liệu.

## 9. Thiết kế cấu trúc dữ liệu (Data Structure Design)
Sử dụng `PriorityQueue` trong Java, `heapq` trong Python, hoặc `std::priority_queue` trong C++. Đối với Java:
- Min-Heap: `PriorityQueue<Integer> minHeap = new PriorityQueue<>();`
- Max-Heap: `PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> b - a);`

## 10. Cài đặt cơ bản (Basic Implementation)
```java
public List<Integer> findKLargest(int[] nums, int k) {
    PriorityQueue<Integer> minHeap = new PriorityQueue<>();
    for (int num : nums) {
        minHeap.add(num);
        if (minHeap.size() > k) {
            minHeap.poll();
        }
    }
    return new ArrayList<>(minHeap);
}
```

## 11. Ví dụ minh họa (Illustrative Example)
Tìm 2 phần tử lớn nhất trong mảng `[3, 1, 5, 12, 2, 11]`. K = 2.
- Min-Heap: 
  - Đưa 3 vào: `[3]`
  - Đưa 1 vào: `[1, 3]`
  - Đưa 5 vào: `[1, 3, 5]` -> Size > 2 -> poll() 1 -> `[3, 5]`
  - Đưa 12 vào: `[3, 5, 12]` -> poll() 3 -> `[5, 12]`
  - Đưa 2 vào: `[2, 5, 12]` -> poll() 2 -> `[5, 12]`
  - Đưa 11 vào: `[5, 11, 12]` -> poll() 5 -> `[11, 12]`
Kết quả: `[11, 12]`.

## 12. Ứng dụng thực tế (Real-world Applications)
- Trending Topics / Hashtags trên Twitter (Top K Frequent).
- Gợi ý sản phẩm mua nhiều nhất (E-commerce).
- Hiển thị K bài viết có lượt tương tác cao nhất.

## 13. Mở rộng (Extensions)
- Kết hợp Heap với Custom Comparator để lưu trữ Objects (ví dụ: `Point(x, y)` tính khoảng cách Euclidean).
- Kết hợp HashMap + Heap cho bài toán tìm Top K phần tử xuất hiện nhiều nhất.

## 14. Các biến thể (Variations)
- Bài toán tìm phần tử thiếu sót/thừa với Heap.
- Hàng đợi ưu tiên kết hợp Sliding Window (cần thao tác xóa phần tử không ở đỉnh - `remove(Object)`).

## 15. 20 câu hỏi phỏng vấn (20 Interview Questions)
1. Sự khác biệt giữa Min-Heap và Max-Heap khi áp dụng Top K pattern là gì?
2. Khi nào nên dùng Min-Heap thay vì Max-Heap?
3. Có thể dùng TreeMap thay cho PriorityQueue trong bài toán Top K không?
4. Quickselect là gì? So sánh hiệu suất của nó với Min-Heap cho bài toán "Kth Largest".
5. Làm sao để áp dụng Top K pattern trên một luồng dữ liệu liên tục (Data Stream)?
6. Time complexity của việc tìm Top K bằng Heap là bao nhiêu? Giải thích.
7. Space complexity tối ưu nhất cho bài toán tìm K phần tử lớn nhất là gì?
8. Điều gì xảy ra nếu mảng đầu vào có ít hơn K phần tử?
9. Nếu K bằng N, độ phức tạp của thuật toán dùng Heap là bao nhiêu?
10. Làm sao để đếm các phần tử "xuất hiện nhiều nhất" (Top K Frequent)?
11. Có thể dùng Heap để sắp xếp các ký tự trong chuỗi theo tần suất không?
12. Làm sao để giải bài "K Closest Points to Origin"?
13. PriorityQueue trong Java được cài đặt bằng cấu trúc dữ liệu nào?
14. Heapify là gì? Tính Time complexity của Heapify.
15. Sự khác biệt giữa việc insert từng phần tử vào Heap và dùng Heapify?
16. Nếu nhiều phần tử có cùng tần suất, làm sao để so sánh chúng trong Heap?
17. Cách xóa một phần tử bất kỳ trong Priority Queue mất bao nhiêu thời gian?
18. Có thể dùng Bucket Sort thay thế Heap trong trường hợp nào?
19. Giải thích bài toán "Find K Pairs with Smallest Sums".
20. Ưu điểm của Min-Heap (size K) so với Max-Heap (size N) để tìm K phần tử lớn nhất?

## 16. 20 lỗi thường gặp (20 Common Bugs)
1. Dùng Min-Heap thay vì Max-Heap (hoặc ngược lại) sai mục đích.
2. Quên không poll() phần tử khi `size > k`.
3. Khởi tạo Kích thước Heap sai, dẫn đến `NullPointerException` hoặc `IndexOutOfBoundsException`.
4. Không xử lý trường hợp `k <= 0`.
5. Không kiểm tra trường hợp `k > nums.length`.
6. Trả về Heap chưa được đảo ngược (nếu đề yêu cầu kết quả sắp xếp giảm dần/tăng dần).
7. Sử dụng PriorityQueue mà quên truyền Custom Comparator khi lưu object phức tạp.
8. So sánh object bằng toán tử `==` thay vì `equals()` hoặc Comparator.
9. Trong bài K Frequent, Heap so sánh giá trị thay vì tần suất đếm được từ HashMap.
10. Quên gọi `Map.getOrDefault` gây lỗi `NullPointerException` khi xây dựng HashMap.
11. Bị Integer Overflow khi tính bình phương khoảng cách (K Closest Points).
12. Viết Custom Comparator bằng `a - b` có thể gây tràn số nguyên (Overflow), nên dùng `Integer.compare(a, b)`.
13. Xóa phần tử bằng `remove(Object)` trong Java Heap tốn $O(N)$ thay vì $O(\log N)$.
14. Thao tác `poll()` trên Heap rỗng gây Exception.
15. Không xử lý phần tử trùng lặp (duplicates) đúng cách theo yêu cầu đề.
16. Thêm null vào PriorityQueue gây `NullPointerException`.
17. Sai thứ tự ưu tiên khi custom sorting nhiều tiêu chí (ví dụ: cùng tần suất thì xếp theo thứ tự từ điển).
18. Trả về kết quả trực tiếp từ iterator của Heap (iterator của Heap không đảm bảo thứ tự).
19. Cố gắng thay đổi (mutate) object đã nằm trong Heap mà không re-heapify.
20. Sót phần tử cuối khi lưu stream do vòng lặp kết thúc sớm.

## 17. 30 trường hợp góc (30 Edge Cases)
1. Mảng rỗng (`[]`).
2. Mảng có 1 phần tử.
3. K = 0 (Không cần phần tử nào).
4. K lớn hơn kích thước mảng (`K > N`).
5. K = N (Lấy toàn bộ phần tử).
6. Mảng chứa toàn các số giống nhau (`[2, 2, 2]`).
7. Mảng đã được sắp xếp tăng dần.
8. Mảng đã được sắp xếp giảm dần.
9. Mảng chứa các số nguyên âm.
10. Mảng chứa số cực lớn / cực nhỏ (Integer.MAX_VALUE, Integer.MIN_VALUE).
11. Tần suất của tất cả các phần tử bằng nhau (Top K Frequent).
12. Có nhiều phần tử chia sẻ vị trí thứ K (ví dụ cùng tần suất hoặc cùng giá trị).
13. Luồng dữ liệu stream mới bắt đầu và kích thước chưa đạt K.
14. Khi yêu cầu sắp xếp chuỗi (Top K Words), các từ rỗng `""`.
15. Các từ có cả chữ hoa và chữ thường.
16. Điểm trùng gốc tọa độ (0, 0) trong K Closest Points.
17. Các điểm có cùng khoảng cách đến gốc.
18. Array kích thước chẵn.
19. Array kích thước lẻ.
20. Mảng chứa các giá trị null.
21. Hash map có size nhỏ hơn K.
22. Tìm K phần tử gần giá trị X nhất, trong đó X có sẵn trong mảng.
23. Tìm K phần tử gần giá trị X nhất, trong đó X KHÔNG có sẵn trong mảng.
24. Custom class không implement interface `Comparable`.
25. Mảng có chứa NaN hoặc Infinity (nếu là số thực).
26. Danh sách có các string trùng lặp.
27. Đếm tần suất với các chữ số thập phân.
28. K có giá trị rất lớn đến mức lưu trữ Heap gây cạn kiệt bộ nhớ.
29. Cần lấy K phần tử từ các ma trận (K pairs with smallest sums) nhưng ma trận bị rỗng.
30. Yêu cầu lấy kết quả từ Heap nhưng K bằng chính xác số lượng Unique elements.

## 18. Các bước tư duy khi giải (Thought Process)
1. Xác định bài toán cần tìm "K phần tử tốt nhất" (Largest/Smallest/Closest).
2. Quyết định loại Heap cần dùng. Ví dụ: Tìm "Lớn nhất" -> Dùng "Min-Heap" kích thước K.
3. Nếu cần phân loại theo tiêu chí đặc biệt (tần suất, khoảng cách), xây dựng cấu trúc phụ trợ (HashMap đếm tần suất) hoặc Object bao bọc.
4. Duyệt từng phần tử của cấu trúc dữ liệu, thêm vào Heap.
5. Kiểm tra kích thước của Heap: Nếu `size > K`, thực hiện `poll()`.
6. Cuối cùng, các phần tử còn lại trong Heap chính là "Top K". Tùy yêu cầu, có thể cần sắp xếp lại (việc pop liên tục sẽ cho kết quả ngược hoặc xuôi).

## 19. Tối ưu hóa (Optimization)
- Sử dụng thuật toán Quickselect cho $O(N)$ thời gian trung bình nếu không cần lấy mảng kết quả đã được sắp xếp hoặc không xử lý luồng (stream).
- Áp dụng Bucket Sort cho các bài toán tần suất nếu giá trị tần suất có giới hạn `[0, N]`. Time Complexity trở thành $O(N)$.

## 20. Câu hỏi trắc nghiệm (Multiple Choice Questions)
1. Để lấy 3 phần tử nhỏ nhất từ mảng, ta nên duy trì:
   - A. Min-Heap kích thước 3
   - B. Max-Heap kích thước 3 (Đáp án đúng)
   - C. Min-Heap kích thước N
2. Kích thước Heap càng lớn thì chi phí thời gian push/pop:
   - A. Càng nhỏ
   - B. Không đổi
   - C. Càng lớn (Đáp án đúng)

## 21. Bài tập thực hành (Practice Exercises)
- Kth Largest Element in an Array (LeetCode 215)
- Top K Frequent Elements (LeetCode 347)
- K Closest Points to Origin (LeetCode 973)

## 22. Bài tập nâng cao (Advanced Problems)
- Find Median from Data Stream (LeetCode 295) - Dùng 2 Heaps.
- Trapping Rain Water II (LeetCode 407) - Dùng Heap để mô phỏng mức nước.

## 23. Tài liệu tham khảo (References)
- "Introduction to Algorithms" (CLRS)
- LeetCode Discuss: Top K Elements pattern

## 24. Tổng kết (Summary)
Pattern Top K Elements sử dụng Priority Queue (Heap) để tối ưu việc tìm kiếm và bảo trì K phần tử nổi bật nhất trong luồng dữ liệu hoặc danh sách có kích thước lớn. Nắm vững việc thiết lập Custom Comparator và sự phân biệt Min/Max Heap là chìa khóa để giải quyết triệt để các bài tập.
