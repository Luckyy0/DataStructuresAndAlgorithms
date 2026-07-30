# Cấu Trúc Dữ Liệu và Thuật Toán: Basic Greedy Concepts (Thuật Toán Tham Lam Cơ Bản)

## 1. Giới thiệu chung (Overview)
Thuật toán tham lam (**Greedy Algorithm**) là một phương pháp tiếp cận thiết kế thuật toán trong đó chúng ta luôn đưa ra lựa chọn tối ưu nhất tại thời điểm hiện tại (local optimum) với hy vọng rằng các lựa chọn này sẽ dẫn đến giải pháp tối ưu toàn cục (global optimum) ở cuối cùng.

## 2. Các khái niệm cốt lõi (Core Concepts)
- **State/Choice:** Tại mỗi bước, chúng ta đứng trước nhiều lựa chọn.
- **Myopic (Thiển cận):** Chỉ quan tâm đến lợi ích trước mắt mà không cần xem xét lại hoặc đánh giá toàn bộ kết quả tương lai.
- **Irrevocable (Không thể thu hồi):** Một khi đã đưa ra lựa chọn, Greedy sẽ không bao giờ quay lại để thay đổi (không giống như Backtracking).

## 3. Local optimum vs Global optimum
- **Local optimum (Tối ưu cục bộ):** Lựa chọn tốt nhất trong một tập hợp con nhỏ các khả năng ở thời điểm hiện tại.
- **Global optimum (Tối ưu toàn cục):** Lựa chọn tốt nhất trong tất cả các khả năng tổng thể giải quyết bài toán.
Greedy thành công khi một chuỗi các **local optimum** sẽ cấu thành một **global optimum**.

## 4. Khi nào sử dụng Greedy (When to use Greedy)
Không phải bài toán nào cũng giải được bằng Greedy. Greedy chỉ áp dụng được nếu bài toán thỏa mãn 2 tính chất bắt buộc:
1. **Optimal Substructure**
2. **Greedy Choice Property**

## 5. Optimal Substructure (Cấu trúc tối ưu con)
Một bài toán có **Optimal Substructure** nếu lời giải tối ưu của bài toán ban đầu có thể được xây dựng dựa trên lời giải tối ưu của các bài toán con (sub-problems) nhỏ hơn.

## 6. Greedy Choice Property (Tính chất lựa chọn tham lam)
Một bài toán có **Greedy Choice Property** nếu một tối ưu toàn cục có thể đạt được bằng cách chọn tối ưu cục bộ ở mỗi bước mà không cần giải các bài toán con trước (khác với DP). Chúng ta có thể an toàn đưa ra lựa chọn greedy mà không cần đánh giá tất cả các trường hợp khác.

## 7. Chứng minh tính đúng đắn (Proving correctness)
Chứng minh thuật toán Greedy thường khó hơn việc nghĩ ra nó. Hai phương pháp phổ biến:
1. **Exchange Argument:** Chứng minh rằng bất kỳ lời giải tối ưu nào cũng có thể biến đổi thành lời giải của Greedy mà không làm giảm kết quả.
2. **Induction (Quy nạp):** Chứng minh tính đúng đắn ở bước đầu tiên, sau đó giả sử đúng ở bước k và chứng minh đúng ở bước k+1.

## 8. Kỹ thuật Exchange Argument
Đây là kỹ thuật mạnh mẽ nhất. Ý tưởng:
- Giả sử có một giải pháp tối ưu $O$ khác với giải pháp do thuật toán Greedy $G$ tạo ra.
- Tìm điểm khác biệt đầu tiên giữa $O$ và $G$.
- Hoán đổi (Exchange) phần tử trong $O$ bằng phần tử trong $G$.
- Chứng minh rằng chi phí hoặc lợi ích của lời giải mới là tốt hơn hoặc bằng $O$. Suy ra $G$ cũng tối ưu.

## 9. Fractional Knapsack vs 0/1 Knapsack
- **Fractional Knapsack (Cái túi chia nhỏ được):** Cho phép lấy một phần của món đồ. Áp dụng Greedy: Sắp xếp các món đồ theo giá trị/trọng lượng (value/weight) giảm dần và lấy nhiều nhất có thể.
- **0/1 Knapsack (Cái túi 0/1):** Mỗi món đồ chỉ được lấy toàn bộ hoặc không lấy. Greedy **thất bại** vì việc lấy món đồ giá trị cao nhưng nặng có thể ngăn việc lấy nhiều món đồ nhỏ tối ưu hơn. Phải dùng DP.

## 10. Cách tiếp cận và giải quyết (Approaches)
1. Phát biểu bài toán và tìm kiếm sự tối ưu.
2. Sắp xếp (Sorting) hoặc sử dụng Hàng đợi ưu tiên (Priority Queue).
3. Duyệt qua dữ liệu, đưa ra lựa chọn tham lam tại mỗi bước.
4. Cập nhật trạng thái và tiếp tục cho đến khi hoàn thành.

## 11. Phân tích độ phức tạp (Complexity)
- **Time Complexity:** Thường là $O(N \log N)$ do cần sắp xếp dữ liệu (Sorting). Nếu dữ liệu đã được sắp xếp, thời gian có thể là $O(N)$.
- **Space Complexity:** Thường là $O(1)$ hoặc $O(N)$ nếu cần không gian lưu trữ cấu trúc sắp xếp hoặc Priority Queue.

## 12. Ứng dụng thực tế (Real-world Applications)
- **Routing Algorithms:** Dijkstra (tìm đường đi ngắn nhất), Prim, Kruskal (Cây khung nhỏ nhất).
- **Data Compression:** Huffman Coding.
- **Scheduling:** Bài toán xếp lịch (Activity Selection).

## 13. Ưu điểm và Nhược điểm (Pros & Cons)
- **Ưu điểm:** Dễ cài đặt, hiệu suất cao, không tiêu thụ nhiều bộ nhớ.
- **Nhược điểm:** Dễ sai, cực kỳ khó chứng minh tính đúng đắn, không thể giải một số bài toán (như 0/1 Knapsack, TSP tổng quát).

## 14. So sánh với Quy hoạch động (Dynamic Programming)
- **Greedy:** Quyết định sớm, không nhìn lại, giải quyết top-down. Thường nhanh hơn nhưng áp dụng hẹp hơn.
- **DP:** Giải quyết các bài toán con overlapping, ghi nhớ kết quả (Memoization/Tabulation), đảm bảo vét cạn thông minh. Thường chậm hơn nhưng bao quát.

## 15. So sánh với Quay lui (Backtracking)
- **Greedy:** Thời gian tuyến tính hoặc n log n. Không quay lại sửa sai.
- **Backtracking:** Có thể khám phá toàn bộ không gian trạng thái (Exponential Time). Quay lui khi nhánh hiện tại không dẫn đến kết quả mong muốn.

## 16. Các dạng bài tập (Problem Patterns)
1. **Activity Selection / Interval Problems:** Xếp lịch, gộp khoảng (Merge Intervals).
2. **Min/Max Array manipulation:** Tối ưu hóa chuỗi hoặc mảng.
3. **Graph Algorithms:** Thuật toán trên đồ thị (Dijkstra, MST).
4. **Data Compression/Encoding.**

## 17. Mẫu mã nguồn (Code Templates)
```java
// Mẫu thông thường: Sắp xếp + Duyệt
Arrays.sort(arr, (a, b) -> a.value - b.value); // Hoặc dùng PriorityQueue
int result = 0;
for (Item item : arr) {
    if (isValid(item)) {
        result += item.cost;
        updateState(item);
    }
}
return result;
```

## 18. 20 Câu hỏi phỏng vấn (20 Interview Questions)
1. Thuật toán Greedy là gì? Nó khác gì với Dynamic Programming?
2. Khi nào thì dùng thuật toán Greedy? Hai thuộc tính bắt buộc là gì?
3. Trình bày bài toán Fractional Knapsack và tại sao Greedy giải được.
4. Tại sao 0/1 Knapsack không thể dùng thuật toán Greedy?
5. Kỹ thuật Exchange Argument là gì? Cung cấp một ví dụ.
6. Huffman Coding hoạt động như thế nào và tại sao nó thuộc lớp bài toán Greedy?
7. Dijkstra's Algorithm có phải là thuật toán tham lam không? Giải thích.
8. Kruskal và Prim algorithm giống và khác nhau ở điểm nào?
9. Thuật toán Greedy có luôn luôn mang lại cấu trúc tối ưu toàn cục không?
10. Bài toán Activity Selection là gì và giải nó như thế nào?
11. Local optimum và Global optimum khác nhau thế nào?
12. Giải thích bài toán Jump Game và vì sao Greedy hiệu quả.
13. Làm sao để phát hiện một bài toán array manipulation cần áp dụng Greedy?
14. Có bài toán nào Greedy đưa ra kết quả sai nhưng heuristics vẫn được dùng không?
15. Độ phức tạp thông thường của thuật toán tham lam trên đồ thị?
16. Nếu một bài toán yêu cầu tìm số lượng đồng xu nhỏ nhất để thối tiền, Greedy luôn đúng không? (Không, phụ thuộc vào hệ thống mệnh giá).
17. Cho ví dụ về một hệ thống tiền tệ mà Greedy Coin Change sẽ thất bại. (VD: 1, 3, 4. Muốn tạo số 6).
18. Trình bày bài toán "Merge Intervals" với cách tiếp cận Greedy.
19. Giải bài toán xếp hàng với thời gian chờ nhỏ nhất bằng Greedy.
20. Scheduling tasks with deadlines and penalties - cách dùng Greedy.

## 19. 20 Lỗi thường gặp (20 Common Bugs)
1. Quên sắp xếp đầu vào (Sorting) trước khi áp dụng Greedy.
2. Sắp xếp tăng dần thay vì giảm dần và ngược lại.
3. Sử dụng sai cấu trúc dữ liệu cho Comparator.
4. Xử lý tràn số (Integer Overflow) khi cộng chi phí.
5. So sánh tỷ lệ giá trị/trọng lượng (Fractional) bằng số thực `double` sinh ra sai số (Floating point precision).
6. Không xử lý được các trường hợp số âm hoặc phần tử có giá trị 0.
7. Áp dụng Greedy cho bài toán bắt buộc phải dùng DP.
8. Quên cập nhật biến trạng thái bên trong vòng lặp.
9. PriorityQueue cập nhật không đúng hoặc thiếu cơ chế re-heapify.
10. Đánh giá sai "Greedy Choice" - chọn sai tiêu chí ưu tiên.
11. Lỗi Index Out of Bounds khi truy xuất phần tử tiếp theo.
12. Khởi tạo giá trị bắt đầu bị sai (ví dụ set min bằng 0 thay vì Integer.MAX_VALUE).
13. Xử lý thiếu phần tử cuối cùng trong mảng.
14. Nhầm lẫn giữa object reference và primitive value trong khi sorting.
15. Gây ra ConcurrentModificationException nếu chỉnh sửa cấu trúc dữ liệu khi đang duyệt.
16. Thay đổi đầu vào gốc không mong muốn (Cần copy array nếu bài yêu cầu giữ nguyên mảng cũ).
17. Điều kiện cắt nhánh vòng lặp (break condition) bị sai.
18. Không xem xét kỹ đến việc phần tử bị lặp (duplicates).
19. Bỏ sót trường hợp rỗng (empty array).
20. Nhầm lẫn logic khi so sánh chéo (cross multiplication) để tránh sai số dấu phẩy động nhưng bị tràn số nguyên.

## 20. 30 Trường hợp biên (30 Edge Cases)
1. Mảng/Danh sách rỗng (Empty input).
2. Mảng có đúng 1 phần tử.
3. Tất cả phần tử đều bằng nhau.
4. Các phần tử đã được sắp xếp sẵn (sorted ascending).
5. Các phần tử đã được sắp xếp ngược (sorted descending).
6. Các phần tử có giá trị âm.
7. Trọng lượng bằng 0, giá trị bằng không trong Fractional Knapsack.
8. Target value bằng 0.
9. Giá trị đầu vào quá lớn (Integer.MAX_VALUE).
10. Số lượng phần tử chẵn/lẻ làm sai lệch bài toán chia đôi.
11. Hệ thống coin change có đồng tiền lớn hơn tổng cần tìm.
12. Có giá trị duplicate gây nhiễu lựa chọn.
13. Khoảng thời gian (Interval) bắt đầu và kết thúc cùng lúc.
14. Các interval bao trùm hoàn toàn lẫn nhau.
15. Không có bất kỳ khoảng hở nào giữa các interval.
16. Số đỉnh đồ thị $V = 1$ trong Dijkstra/MST.
17. Đồ thị không liên thông khi tìm MST.
18. Đồ thị có chu trình âm (Dijkstra fail).
19. Phân chia k mảng con với k > số lượng phần tử.
20. Phân chia mảng con với k = 1.
21. Bài toán Jump Game: Phần tử đầu tiên là 0.
22. Bài toán Jump Game: Độ dài mảng lớn, các phần tử đều là 1.
23. Không thể thoả mãn yêu cầu bằng bất kỳ cấu hình nào (Return -1/false).
24. Có nhiều lời giải cùng chi phí nhưng yêu cầu trả về theo thứ tự từ điển.
25. Mảng có nhiều phần tử lớn có thể gây lỗi Overflow trong lúc tính tích/tổng.
26. Mảng String độ dài cực lớn với ký tự giống nhau.
27. Đếm số lần hoạt động với thời gian thực thi bằng 0.
28. Tổng giá trị của tất cả items vừa khít sức chứa.
29. Cần tối ưu kết quả theo chiều ngang (số lượng) thay vì chiều sâu (chất lượng).
30. Dữ liệu rác (null pointer) xen kẽ trong object arrays.

## 21. Chiến lược làm bài (Test Taking Strategy)
- Thử suy luận bằng Greedy trước khi dùng DP vì Greedy code nhanh.
- Luôn thử phản chứng (Counter-example) trong đầu. Nếu tìm được 1 testcase làm Greedy sai, hãy chuyển sang DP hoặc Backtracking.
- Chú ý đến việc sorting mảng. Nhiều bài toán greedy sáng tỏ ngay sau khi sắp xếp.

## 22. Debug và Test (Debugging & Testing)
- Tạo test cases có phần tử trùng lặp.
- Đảo ngược thứ tự input để xem kết quả có bị phụ thuộc thứ tự không.
- Breakpoint vào phần update biến greedy để kiểm tra lựa chọn cục bộ có thực sự tốt không.

## 23. Tài liệu tham khảo (References)
- Introduction to Algorithms (CLRS) - Chapter 16: Greedy Algorithms.
- CP-Algorithms: Greedy Algorithms.
- LeetCode Discuss & Solutions.

## 24. Tổng kết (Summary)
Greedy là một chiến thuật đơn giản, dễ cài đặt nhưng cực kỳ nguy hiểm nếu chưa được chứng minh đúng đắn. Việc nhận diện hai tính chất "Optimal Substructure" và "Greedy Choice Property" là chìa khóa để làm chủ thuật toán này trong các kỳ phỏng vấn.
