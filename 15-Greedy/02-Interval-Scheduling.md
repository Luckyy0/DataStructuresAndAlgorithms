# Bài 2: Interval Scheduling (Lập Lịch Khoảng Thời Gian)

## 1. Giới thiệu (Introduction)
**Interval Scheduling** là một lớp các bài toán tối ưu hóa trong khoa học máy tính, xoay quanh việc quản lý, phân bổ và xử lý một tập hợp các khoảng thời gian (intervals). Các bài toán này thường áp dụng chiến lược **Greedy (Tham lam)** để đạt được giải pháp tối ưu.

## 2. Định nghĩa (Definition)
Một **Interval** (khoảng) được định nghĩa bởi một cặp giá trị `[start, end]`, trong đó `start` là thời gian bắt đầu và `end` là thời gian kết thúc của một sự kiện (event), công việc (task), hoặc cuộc họp (meeting). Thường thì $start \le end$.

## 3. Ứng dụng thực tế (Real-world Applications)
- **Lập lịch phòng họp (Meeting Room Scheduling)**: Tối ưu hóa số lượng phòng hoặc sắp xếp tối đa số cuộc họp.
- **Phân bổ tài nguyên hệ điều hành (OS Resource Allocation)**: Lập lịch các tiến trình CPU.
- **Mạng viễn thông (Telecommunications)**: Cấp phát băng thông trong các khung thời gian.
- **Đặt chỗ (Booking Systems)**: Lịch đặt chuyến bay, lịch lưu trú khách sạn.

## 4. Cách biểu diễn Interval (Interval Representation)
Trong Java, một interval thường được biểu diễn bằng mảng một chiều 2 phần tử `int[] interval = {start, end}` hoặc một lớp (class) tự định nghĩa `class Interval { int start; int end; }`. Tập hợp các intervals là một mảng hai chiều `int[][]` hoặc danh sách `List<int[]>`.

## 5. Sắp xếp Interval theo Start Time (Sorting by Start Time)
Khi sắp xếp các intervals theo **start time** (thời gian bắt đầu) tăng dần, ta dễ dàng xử lý các bài toán liên quan đến việc **gộp (merge)** các intervals hoặc tìm thời điểm có nhiều intervals chồng chéo nhất (dùng thêm Min-Heap).
```java
Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
```

## 6. Sắp xếp Interval theo End Time (Sorting by End Time)
Sắp xếp theo **end time** (thời gian kết thúc) tăng dần là chìa khóa cho các bài toán muốn chọn được **nhiều intervals không chồng chéo nhất** (ví dụ: Activity Selection). Lý do là kết thúc càng sớm, ta càng có nhiều thời gian cho các intervals tiếp theo.
```java
Arrays.sort(intervals, (a, b) -> Integer.compare(a[1], b[1]));
```

## 7. Bài toán Activity Selection (Activity Selection Problem)
**Nguyên tắc**: "Luôn chọn hoạt động kết thúc sớm nhất (earliest finishing time)". 
- Sắp xếp tất cả các hoạt động theo `end time`.
- Khởi tạo biến lưu `end time` của hoạt động cuối cùng được chọn.
- Duyệt qua mảng: nếu `start time` của hoạt động hiện tại $\ge$ `end time` đang lưu, ta chọn hoạt động đó và cập nhật lại `end time`.

## 8. Logic gộp các Interval lặp nhau (Merging Overlapping Intervals)
Hai intervals `A = [s1, e1]` và `B = [s2, e2]` (giả sử $s1 \le s2$) sẽ chồng chéo (overlap) nếu $s2 \le e1$.
- **Merge logic**: Gộp lại thành một interval mới `[s1, Math.max(e1, e2)]`.
- Bài toán phổ biến: Cho một danh sách các intervals, gộp tất cả các intervals chồng chéo. Yêu cầu sắp xếp theo `start time` trước.

## 9. Thuật toán Greedy cho Interval (Greedy Algorithm for Intervals)
Chiến lược **Greedy** trong bài toán interval dựa trên việc đưa ra quyết định tốt nhất tại từng bước cục bộ (chẳng hạn kết thúc sớm nhất hoặc bắt đầu sớm nhất) để hi vọng đạt được kết quả tối ưu toàn cục. Các bài toán dạng này thường yêu cầu chứng minh tính đúng đắn của Greedy choice.

## 10. Kỹ thuật Line Sweeping cơ bản (Basic Line Sweeping)
**Sweep Line (Đường quét)** là kỹ thuật phân tách mỗi interval `[start, end]` thành 2 sự kiện: `+1` tại `start` và `-1` tại `end`. 
Sắp xếp tất cả các điểm kiện theo thời gian. Duyệt qua mảng sự kiện và tính tổng cộng dồn để biết số lượng intervals hoạt động tại một thời điểm.

## 11. So sánh Start Time vs End Time (Comparison: Start vs End Time)
| Tiêu chí | Sort by Start Time | Sort by End Time |
|----------|---------------------|-------------------|
| Ưu tiên dùng | Merge Intervals, Meeting Rooms | Non-overlapping Intervals, Max Activities |
| Đặc điểm | Theo thứ tự thời gian bắt đầu | Tối đa hóa khoảng trống sau đó |
| Cấu trúc đi kèm | Thường kết hợp Min-Heap (Priority Queue) | Thường chỉ cần duyệt mảng đơn giản (Greedy) |

## 12. Mã nguồn cơ bản (Basic Implementation)
Mẫu code duyệt và xử lý interval cơ bản:
```java
Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
List<int[]> result = new ArrayList<>();
int[] prev = intervals[0];
for (int i = 1; i < intervals.length; i++) {
    int[] curr = intervals[i];
    if (curr[0] <= prev[1]) { // Overlap
        prev[1] = Math.max(prev[1], curr[1]);
    } else { // No overlap
        result.add(prev);
        prev = curr;
    }
}
result.add(prev);
```

## 13. Độ phức tạp thời gian (Time Complexity)
- **Sắp xếp (Sorting)**: Đa số bài toán yêu cầu sắp xếp danh sách ban đầu, mất $O(N \log N)$.
- **Duyệt (Traversal)**: Sau khi sắp xếp, thường chỉ cần một vòng lặp qua mảng, mất $O(N)$.
- Tổng Time Complexity: $O(N \log N)$.

## 14. Độ phức tạp không gian (Space Complexity)
- Mảng kết quả (Result List): $O(N)$ nếu tất cả khoảng phân biệt.
- Không gian Sắp xếp (Sorting Space): $O(\log N)$ tới $O(N)$ tùy thuộc vào thuật toán sort của ngôn ngữ.
- Tổng Space Complexity: $O(N)$ trong trường hợp xấu nhất.

## 15. Ưu điểm (Advantages)
- Tiếp cận trực quan, dễ hiểu bằng hình vẽ trên trục số.
- Khi đã nhận diện được dấu hiệu, mẫu thuật toán thường cố định và ít thay đổi.
- Kỹ thuật Greedy rất hiệu quả về thời gian chạy thực tế ($O(N \log N)$).

## 16. Nhược điểm (Disadvantages)
- Việc phân biệt nên sắp xếp theo `start` hay `end` đôi khi gây nhầm lẫn.
- Với các bài toán có yêu cầu lưu giữ giá trị của khoảng (weight), Greedy có thể sai và phải dùng Dynamic Programming.

## 17. Khi nào nên dùng (When to Use)
- Dấu hiệu: Bài toán cho một mảng các khoảng thời gian, công việc, lịch trình (`[start, end]`).
- Yêu cầu: Gộp các khoảng, tìm khoảng trống, đếm số phòng/nhân sự tối thiểu, tối đa số lượng công việc không lặp.

## 18. Khi nào không nên dùng (When Not to Use)
- Khi các intervals có thêm thuộc tính **giá trị (weight/profit)** và bài toán yêu cầu tìm tổng giá trị lớn nhất của các khoảng không giao nhau. Lúc này phải dùng thuật toán **Weighted Job Scheduling** (Sắp xếp + Binary Search + DP).

## 19. So sánh với các thuật toán khác
- **Interval Greedy vs Dynamic Programming**: Greedy đưa ra quyết định dựa vào tính chất liền kề (start/end) không quay lui, DP duyệt qua các cấu hình để tìm giá trị tối ưu.
- **Interval vs Graph (Đồ thị)**: Một tập các intervals có thể coi là **Interval Graph**. Bài toán Meeting Rooms chính là tìm Chromatic Number (số màu) của đồ thị đó.

## 20. Mẫu Code (Code Templates)
*Xem kỹ phần 12 và File bài tập để nắm mẫu code.*

## 21. 20 Câu hỏi phỏng vấn (20 Interview Questions)
1. Activity Selection Problem: Hãy chỉ ra vì sao sắp xếp theo end time luôn mang lại số hoạt động tối đa.
2. Làm sao gộp các overlapping intervals trong $O(N \log N)$?
3. Nếu các intervals đã được sắp xếp sẵn, độ phức tạp của bài toán merge là bao nhiêu?
4. Bài toán Meeting Rooms II: Cách sử dụng Priority Queue để giải.
5. Bài toán Meeting Rooms II: Cách dùng hai mảng (start mảng, end mảng) để giải mà không cần Priority Queue.
6. Làm sao để chèn thêm một interval mới vào một danh sách các intervals đã được sort?
7. Minimum Number of Arrows to Burst Balloons: Tại sao có thể so sánh bài toán này với việc tìm các tập giao nhau nhỏ nhất?
8. Tại sao việc vượt giới hạn integer (integer overflow) lại xảy ra khi viết comparator `a[0] - b[0]`?
9. Thuật toán phân tách Sweep Line ứng dụng trong bài toán Interval List Intersections như thế nào?
10. Làm sao để tìm các khoảng thời gian "trống" giữa mọi nhân viên (Employee Free Time)?
11. Giải thích sự khác biệt giữa bài toán Non-overlapping Intervals và Burst Balloons.
12. Có thể gộp intervals bằng đệ quy không?
13. Nếu có dữ liệu stream các intervals tới liên tục, làm sao để merge chúng? (Data Stream as Disjoint Intervals)
14. Cấu trúc dữ liệu nào phù hợp nhất để query số lượng intervals giao tại một điểm? (Segment Tree/Fenwick Tree).
15. Remove Covered Intervals: Khi sắp xếp theo start tăng dần và end giảm dần, logic xóa là gì?
16. Teemo Attacking: Tính tổng thời gian trúng độc, bản chất có giống merge intervals không?
17. Maximum Length of Pair Chain: Tại sao lại là Activity Selection Problem biến tướng?
18. Video Stitching có phải thuần túy Interval Scheduling Greedy không hay cần thêm DP?
19. Cách tối ưu để lưu trữ interval nếu start, end thuộc một giới hạn rất nhỏ (ví dụ 1 đến 100)?
20. Tại sao trong Meeting Rooms II, heap lại lưu thời gian kết thúc của các phòng?

## 22. 20 Lỗi phổ biến (20 Common Bugs)
1. Dùng phép trừ `a[0] - b[0]` trong `Comparator` dẫn đến Integer Underflow/Overflow. Luôn dùng `Integer.compare()`.
2. Không cập nhật `prev[1]` bằng giá trị lớn nhất trong phép merge: `prev[1] = Math.max(prev[1], curr[1])`.
3. Sắp xếp sai tiêu chí: Sắp xếp theo `start time` khi đáng lẽ phải dùng `end time` và ngược lại.
4. Xử lý thiếu phần tử cuối cùng sau khi rời vòng lặp.
5. Gặp lỗi NullPointerException nếu mảng `intervals` rỗng hoặc bằng null.
6. So sánh nhầm khoảng (Interval Bắt đầu và Kết thúc tại cùng một thời điểm: `[1, 2]` và `[2, 3]` có giao nhau không? Tùy đề bài).
7. Sắp xếp nhưng không xét tới trường hợp start time bằng nhau (ví dụ: `Remove Covered Intervals` cần sort end time giảm dần nếu start bằng nhau).
8. Sử dụng Sweep line nhưng không gộp các sự kiện xảy ra cùng lúc dẫn tới sai tổng trạng thái tại điểm kiện.
9. Quên xóa phần tử hoặc đánh dấu đã thăm trong các bài toán lặp vòng.
10. Không tính đến khả năng array chứa array có độ dài khác 2.
11. Trong PriorityQueue, nhầm lẫn giữa Min-Heap và Max-Heap.
12. Sweep Line: Trừ điểm trước khi cộng (`-1` đặt trước `+1`) tại các thời điểm giống nhau (Tùy yêu cầu giao có tính chạm biên không).
13. Bỏ qua intervals bị lồng hoàn toàn bên trong một interval khác.
14. Duyệt bằng Iterator của List nhưng lại thực hiện thay đổi Collection gây `ConcurrentModificationException`.
15. Không cẩn thận copy giá trị trong Java (tham chiếu) dẫn đến thay đổi luôn mảng gốc của bài toán khi chưa cho phép.
16. Nhầm lẫn khi tìm kiếm nhị phân (Binary Search) trên mảng các Intervals.
17. Quên break khỏi vòng lặp sớm khi đã tìm đủ (Optimization bug).
18. Tính toán sai giá trị chồng chéo: Lấy `Math.min` thay vì `Math.max`.
19. Khởi tạo mảng trả về sai kích thước, thường nên dùng `ArrayList` sau đó convert sang `int[][]`.
20. Logic lồng nhau (Nested `if-else`) quá phức tạp gây sai sót các điều kiện edge case.

## 23. 30 Edge Cases (Trường hợp đặc biệt)
1. Mảng `intervals` hoàn toàn rỗng `[]`.
2. Mảng chỉ chứa 1 interval `[[1, 5]]`.
3. Có nhiều intervals giống hệt nhau `[[1,2], [1,2], [1,2]]`.
4. Không có bất kỳ intervals nào giao nhau `[[1,2], [3,4], [5,6]]`.
5. Tất cả các intervals đều chồng chéo lẫn nhau.
6. Có một interval khổng lồ bao trùm toàn bộ các intervals khác `[[1,100], [2,3], [4,5]]`.
7. Các khoảng chỉ chạm nhau ở 1 điểm (ví dụ: `[1,2]` và `[2,3]`).
8. Các khoảng có dạng một điểm duy nhất (Point interval) `[5,5]`.
9. Interval có tọa độ âm `[-10, -5]`.
10. Tọa độ của khoảng rất lớn (cỡ $10^9$, `Integer.MAX_VALUE`).
11. Các intervals tạo thành một hình bậc thang lồng nhau `[[1,10], [2,9], [3,8]]`.
12. Hai khoảng có start time giống hệt nhau nhưng end time khác nhau.
13. Hai khoảng có end time giống hệt nhau nhưng start time khác nhau.
14. Các khoảng được cung cấp đã được sắp xếp sẵn ngược chiều.
15. Nhiều khoảng rời rạc nằm đan xen với các cụm khoảng chồng chéo.
16. Số lượng khoảng vô cùng lớn ($10^5$ phần tử) có nguy cơ timeout.
17. Dữ liệu stream tới không theo thứ tự (Bài toán Disjoint Intervals).
18. Các khoảng chứa thời gian kết thúc trước thời gian bắt đầu (Invalid input - nếu đề không bẫy).
19. Nhu cầu thêm/xóa khoảng liên tục (Segment Tree/BST là cần thiết).
20. Bài toán dạng lưới có nhiều đối tượng intervals riêng rẽ (ví dụ: nhiều loại nhân viên khác nhau).
21. Bài toán Meeting Room: Số lượng phòng hiện tại cần phải được thu hồi đúng lúc bằng Priority Queue.
22. Các event có cùng mốc thời gian: Một phòng hợp trả lúc 10:00 và một họp mới bắt đầu 10:00.
23. Bài toán Insert Interval: Interval mới nằm hoàn toàn ngoài danh sách (nhỏ hơn tất cả).
24. Bài toán Insert Interval: Interval mới nằm hoàn toàn ngoài danh sách (lớn hơn tất cả).
25. Bài toán Insert Interval: Interval mới trùm lên tất cả.
26. Arrow Burst Balloon: Giao nhau ở một điểm có được tính là chung một mũi tên không? (Có).
27. Đếm số khoảng trống (Free time): Giữa hai interval liên tiếp không có khoảng trống.
28. Tính tổng chiều dài: Interval có chiều dài bằng 0 (ví dụ `[2, 2]`).
29. Cập nhật dữ liệu hàng ngày (Day-to-day timeline overlap).
30. Yêu cầu tính stable - thứ tự mảng kết quả với các interval không giao nhau không được đảo lộn.

## 24. Tổng kết (Conclusion)
Dạng bài tập **Interval Scheduling** mang tính biểu tượng cao cho phương pháp Greedy (và đôi khi là Sweep Line). Chìa khóa thành công là làm chủ kỹ thuật **Sorting (sắp xếp theo tiêu chí đúng)**, hiểu cách **duyệt mảng kèm biến lưu trữ (prev/last)**, và cẩn thận quản lý các trường hợp giới hạn. Nắm vững dạng này sẽ giúp bạn dễ dàng vượt qua các câu hỏi liên quan đến lịch trình, event trong phỏng vấn kỹ thuật.
