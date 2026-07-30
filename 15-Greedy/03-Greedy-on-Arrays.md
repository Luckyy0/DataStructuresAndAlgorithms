# Greedy on Arrays (Thuật toán Tham lam trên Mảng)

## 1. Giới thiệu tổng quan (Overview)
Thuật toán tham lam (Greedy algorithm) trên mảng (Arrays) là một kỹ thuật tư duy cốt lõi trong các cuộc phỏng vấn. Thay vì thử mọi khả năng (Backtracking) hay chia nhỏ và lưu trữ trạng thái (Dynamic Programming), thuật toán Greedy luôn thực hiện sự lựa chọn tốt nhất tại từng bước (local optimum) với hy vọng đạt được giải pháp tối ưu toàn cục (global optimum).

## 2. Các khái niệm cốt lõi (Core Concepts)
- **Local Optimum (Tối ưu cục bộ):** Quyết định tốt nhất tại thời điểm hoặc phần tử hiện tại.
- **Global Optimum (Tối ưu toàn cục):** Kết quả tối ưu cho toàn bộ bài toán.
- **Greedy Choice Property:** Khả năng của bài toán để đạt được kết quả tối ưu toàn cục chỉ thông qua các lựa chọn tối ưu cục bộ.

## 3. Greedy Traversal Logic (Logic duyệt tham lam)
Quá trình duyệt (traversal) qua mảng thường quyết định cách tiếp cận Greedy:
- **Left-to-right pass (Duyệt từ trái sang phải):** Đây là cách tiếp cận phổ biến nhất, duyệt mảng 1 lần từ đầu tới cuối, liên tục duy trì trạng thái tích lũy (ví dụ: lượng xăng còn lại, số bước xa nhất nhảy được).
- **Right-to-left pass (Duyệt từ phải sang trái):** Duyệt ngược thường dùng khi trạng thái của phần tử hiện tại có tính chất phụ thuộc vào phần tử nằm sau nó.
- **Both passes (Duyệt hai chiều):** Một số bài toán (như *Candy*) yêu cầu tính toán dựa trên điều kiện của cả 2 bên trái và phải. Thông thường ta sẽ giải quyết bằng cách duyệt một lần từ trái sang phải, sau đó duyệt thêm một lần từ phải sang trái.

## 4. Keeping Track of Maximum Reachable States (Theo dõi trạng thái tối đa có thể đạt tới)
Trong các bài toán thuộc dạng *Jump Game*, chiến lược Greedy dựa trên việc liên tục cập nhật khả năng vươn xa nhất có thể đạt được:
- Tại mỗi vị trí `i`, tầm với xa nhất từ vị trí này là `i + nums[i]`.
- Cập nhật biến theo dõi: `maxReachable = max(maxReachable, i + nums[i])`.
- Nếu vị trí hiện tại `i > maxReachable`, tức là vị trí này không thể nào tiếp cận được từ các bước trước đó (dead end).

## 5. Accumulating Differences (Tích lũy chênh lệch)
Kỹ thuật này cực kỳ thông dụng trong các bài toán tối đa hóa lợi nhuận nhiều lần (ví dụ: *Stock Buy and Sell II*).
- Chỉ quan tâm đến phần chênh lệch dương (positive differences) giữa 2 phần tử liên tiếp.
- Phương trình tích lũy: `profit += max(0, nums[i] - nums[i-1])`.

## 6. Các dạng bài toán phổ biến (Common Problem Patterns)
- **Bài toán di chuyển/nhảy (Jump games):** Tập trung vào việc mở rộng "khoảng đạt tới".
- **Bài toán chia/phân bổ (Distribution):** Phân bổ số lượng sao cho thỏa mãn điều kiện ưu tiên (Candy, Gas Station).
- **Bài toán mảng khoảng (Intervals trên mảng 1D):** Tìm vùng phủ sóng tối thiểu/tối đa (Taps, Video Stitching).
- **Bài toán mua bán (Stock transactions):** Theo dõi chênh lệch giá qua từng ngày.

## 7. Hướng dẫn từng bước (Step-by-step Guide)
1. Xác định bài toán có thỏa mãn tính chất tham lam (Greedy property) không.
2. Quyết định hướng duyệt mảng: trái sang, phải sang, hay bắt buộc hai chiều.
3. Xác định trạng thái (state) cần duy trì: max/min/sum (tổng).
4. Viết mã nguồn duyệt qua mảng và cập nhật biến trạng thái cục bộ ở mỗi bước.
5. Kiểm tra tính đúng đắn trên một tập hợp các test case (đặc biệt là edge cases).

## 8. Phân tích độ phức tạp (Complexity Analysis)
- **Time Complexity:** $O(N)$ do duyệt qua mảng một (hoặc vài) lần tuyến tính. Đôi khi là $O(N \log N)$ nếu có yêu cầu phân loại (Sorting) ban đầu.
- **Space Complexity:** $O(1)$ vì chỉ cần khởi tạo vài biến cục bộ để lưu trạng thái, trừ các bài thiết kế theo thuật toán Two-pass yêu cầu mảng lưu trữ tạm $O(N)$.

## 9. Ưu điểm và nhược điểm (Pros & Cons)
- **Ưu điểm:** Hiệu suất cực kỳ nhanh, tiết kiệm bộ nhớ tối đa, dễ triển khai bằng code nếu hiểu được ý tưởng.
- **Nhược điểm:** Việc chứng minh tính đúng đắn (correctness) rất khó khăn. Nếu bài toán không có tính tối ưu tham lam, thuật toán sẽ cung cấp một giải pháp sai hoàn toàn.

## 10. So sánh với Dynamic Programming (Comparison with DP)
- **Greedy:** Luôn nhìn về phía trước và đưa ra một quyết định cố định không bao giờ xem xét lại. Không lưu lại toàn bộ trạng thái bài toán con ($O(1)$ Space).
- **DP:** Xem xét tất cả các lựa chọn khả dĩ, lưu lại các giải pháp con trong bảng nhớ, rồi sau đó chọn ra đường đi tốt nhất ($O(N)$ hoặc $O(N^2)$ Space).

## 11. 20 Câu hỏi phỏng vấn phổ biến (20 Interview Questions)
1. What makes a problem solvable by Greedy instead of DP?
2. Explain the jump game logic. How do you find the minimum jumps?
3. In the Gas Station problem, why does the starting point reset if the sum drops below zero?
4. How do you approach the Candy problem with a two-pass algorithm?
5. How can you buy and sell stocks multiple times to maximize profit?
6. Can Maximum Subarray be considered a Greedy approach?
7. Explain the two-pointer greedy logic in Container With Most Water.
8. How do you sort arrays before applying Greedy in interval-like problems?
9. When would a Greedy approach fail in a Coin Change problem?
10. How do you handle non-contiguous sequences with Greedy?
11. What is the complexity of a typical Array Greedy solution?
12. Can we use Greedy for minimum operations to make array elements equal?
13. How to solve "Can Place Flowers" greedily?
14. Explain "Minimum Number of Taps to Open to Water a Garden".
15. Is Dijkstra's algorithm greedy? How does it apply to arrays?
16. How does sorting help Greedy algorithms?
17. What is the sliding window approach versus Greedy?
18. How to solve greedy problems involving strings mapped to arrays?
19. How do you prove a greedy strategy works? (Exchange argument).
20. Compare Greedy vs Backtracking on array permutations.

## 12. 20 Lỗi phổ biến (20 Common Bugs)
1. Cập nhật `maxReachable` ở sai vị trí (trước hoặc sau khi so sánh).
2. Không khởi tạo các biến tích lũy tổng đúng cách (thường phải là 0).
3. Quên kiểm tra và xử lý lỗi Bounds Checking khi truy xuất `nums[i-1]`.
4. Lựa chọn tham lam bị thiển cận (chỉ nhìn 1 bước nhưng bỏ qua hệ quả toàn cục).
5. Bỏ quên điều kiện chặn đứng `if (i > maxReachable)` gây ra lặp vô hạn hoặc đánh giá sai.
6. Lặp lại việc mua bán chứng khoán trong cùng một ngày, vi phạm điều kiện ràng buộc.
7. Áp dụng sai Greedy cho các bài toán bản chất là DP (Coin Change không chuẩn).
8. Bỏ qua việc kiểm tra nếu mảng có kích thước $N=1$.
9. Tính tổng các chênh lệch bị tràn số (Integer Overflow).
10. Sắp xếp mảng sai tiêu chí, làm cho logic Greedy theo sau bị hỏng.
11. Trong lỗi duyệt 2 lần (Two-pass), quên gộp kết quả bằng hàm `max()`.
12. Vô tình sửa đổi (mutate) mảng dữ liệu gốc khi chưa có bản sao lưu, phá vỡ dữ liệu ban đầu.
13. Gas Station: Quên việc phải đối chiếu tổng xăng và tổng chi phí ở cuối chu trình.
14. Lỗi chênh lệch Index-by-1 (+1 hoặc -1 sai lệch) khi tính khoảng cách phần tử.
15. Không đặt lại giá trị tạm thời về ban đầu khi chạm ngưỡng tổng âm.
16. Lạm dụng phép Sorting trong khi có thể giải bằng $O(N)$ làm tăng thời gian thực thi lên $O(N \log N)$.
17. Trong Two-pass, nhầm lẫn việc so sánh `nums[i]` với `nums[i-1]` và `nums[i+1]`.
18. Trả về kết quả ngầm định sai khi Input là mảng rỗng `[]`.
19. Thiết lập sai cờ Boolean để kiểm soát các trạng thái nhảy ngắt quãng.
20. Quên xử lý logic "dọn dẹp" tại phần tử cuối cùng của vòng lặp.

## 13. 30 Trường hợp biên (30 Edge Cases)
1. Mảng Input hoàn toàn rỗng.
2. Mảng có kích thước chính xác là 1 phần tử.
3. Toàn bộ tất cả phần tử trong mảng có giá trị bằng nhau.
4. Mảng đã được sắp xếp tăng dần tuyệt đối.
5. Mảng đã được sắp xếp giảm dần tuyệt đối.
6. Mảng chứa nhiều giá trị âm đan xen ngẫu nhiên.
7. Mảng chỉ toàn là các số 0 (Zero arrays).
8. Mảng có chứa giá trị cực đại (`Integer.MAX_VALUE`).
9. Biến `maxReachable` không vượt qua được vị trí index `1` (bị tắc nghẽn).
10. Tổng tài nguyên (`gas`) bằng chính xác lượng cần dùng (`cost`).
11. Tác động thay đổi nằm ở điểm cực đoan đầu mảng (Index 0).
12. Tác động thay đổi nằm ở điểm cực đoan cuối mảng (Index N-1).
13. Chuỗi biến động chênh lệch giá trị lớn: `[1, 100, 1, 100]`.
14. Kích thước mảng đạt ngưỡng lớn nhất, có thể gây ra hiện tượng Time Limit Exceeded nếu code dưới $O(N^2)$.
15. Các phần tử liên tiếp giống nhau phá vỡ điều kiện bất đẳng thức ngặt (`<` hoặc `>`).
16. Bài toán chèn khoảng trống (Can Place Flowers) có yêu cầu chèn vào các ô liền kề vi phạm luật.
17. Khoảng cách nhảy chỉ bằng chính chiều dài mảng (vừa đủ tới đích).
18. Chia kẹo (Candy) cho những người có điểm số (ratings) hoàn toàn bằng nhau.
19. Bài toán chứng khoán nhưng giá chỉ mang xu hướng giảm đều đặn.
20. Bài toán chứng khoán với xu hướng tăng vọt một chiều.
21. Phạm vi bao phủ (Coverage) vòi nước bị rách/thủng đoạn giữa mảng.
22. Phạm vi bao phủ trùng lặp hoàn toàn tại một khoảng.
23. Giao điểm tạo diện tích lớn nhất (Container Water) nằm giữa 2 cột có độ cao chênh lệch khổng lồ.
24. Một đỉnh núi cực trị duy nhất nằm ở trung tâm của mảng.
25. Delta (độ chênh lệch) luôn là số lẻ liên tiếp so với số chẵn.
26. Mảng chứa các chu kỳ giá trị lặp lại dạng sóng.
27. Tính tổng dẫn đến Overflow và đòi hỏi chuyển đổi sang `long`.
28. Bài toán khởi tạo duyệt tại một Index ngẫu nhiên thay vì Index 0.
29. Cần tối ưu khoảng cách đến tất cả phần tử khác (Dùng Median).
30. Đầu vào là chuỗi luồng xử lý (Streaming Data), không có chiều dài mảng xác định trước.

## 14. Các kỹ thuật kết hợp (Combined Techniques)
- **Greedy + Sorting:** Đa phần các bài toán Greedy khó yêu cầu sắp xếp trước (theo Start time, End time hoặc khoảng chênh lệch).
- **Greedy + Two Pointers:** Thích hợp để xử lý các bài toán ở 2 đầu mảng hội tụ về giữa.
- **Greedy + Hash Map/Priority Queue:** Sử dụng lưu trữ phụ để luôn ưu tiên chọn phần tử có lợi nhất tức thời (tần suất lớn nhất, giá trị bé nhất).

## 15. Tối ưu hóa bộ nhớ (Memory Optimization)
Trong các bài Two-pass, cố gắng tối ưu từ $O(N)$ về $O(1)$. Ví dụ: Ở bài Candy, thay vì tạo 2 mảng `left` và `right`, ta có thể rút gọn số lượng mảng và kết hợp bằng một vòng lặp kết hợp biến cộng dồn. Các bài Jump Games cũng chỉ nên dùng 2 đến 3 biến cục bộ thay vì mảng phụ lưu trạng thái.

## 16. Tối ưu hóa thời gian (Time Optimization)
Nhận biết sớm khi thuật toán không thể giải quyết bài toán để Early Exit (Ví dụ tổng nguyên liệu âm từ nửa đầu thì Break luôn). Áp dụng thủ thuật trỏ Index thay vì mảng tìm kiếm để giảm thời gian từ $O(N^2)$ xuống $O(N)$.

## 17. Design Patterns
Mặc dù OOP Design Patterns không thường xuất hiện trong thuật toán, nhưng **State Pattern** hoặc **Strategy Pattern** có thể áp dụng nếu cách chọn tiêu chí Greedy có sự biến thiên liên tục trong một ứng dụng lớn.

## 18. Các ví dụ ứng dụng thực tế (Real-world Applications)
- **Hệ thống Routing / GPS:** Lựa chọn hướng đi tối ưu để giảm tải giao thông trong khoảng thời gian ngắn.
- **Load Balancing (Cân bằng tải):** Phân bổ Server tức thời theo tải.
- **Tài chính và Chứng khoán (Finance):** Bot Trading tối đa hóa lợi nhuận vi mô từ chênh lệch giá liên tục.
- **Phân bổ tài nguyên (Resource Allocation):** Cấp phát đường truyền, tần số vô tuyến hợp lý.

## 19. Debugging Greedy Algorithms trên mảng
- Lập Bảng theo dõi (Trace Table) theo dõi các trạng thái biến qua từng Step duyệt `i`.
- Vẽ đồ thị/biểu đồ dạng thanh Bar chart (cực kỳ tốt cho bài Stock và Container with Most Water).
- Áp dụng kiểm thử chéo (Cross-Testing): Chạy thuật toán Brute Force cùng lúc và kiểm chứng tính đồng nhất kết quả.

## 20. Các thư viện hỗ trợ (Support Libraries)
- `Arrays.sort()` (Java), `std::sort()` (C++).
- Sử dụng cấu trúc lưu trữ `PriorityQueue` để hỗ trợ chọn lọc tính tham lam động liên tục.

## 21. Bài tập thực hành (Practice Exercises)
Bạn cần luyện tập khoảng 20 - 30 bài để phát triển nhạy cảm "Trực giác tham lam". Tham khảo file danh sách bài tập.

## 22. Lời khuyên cho ứng viên phỏng vấn (Interview Tips)
- Đừng vội code ngay thuật toán tham lam nếu bạn không chắc nó đúng. Hãy nêu ý tưởng trước tiên.
- Khéo léo nhắc về cách chứng minh bằng thuật ngữ "Exchange Argument" (Nếu tôi tráo đổi với phương án khác, nó có làm tình hình xấu đi không?).
- Nhấn mạnh điểm mạnh $O(1)$ Space của Greedy so với Dynamic Programming.

## 23. Tài liệu tham khảo (References)
- Sách *Introduction to Algorithms (CLRS)*.
- Mục lục Greedy và Array trên LeetCode/HackerRank.

## 24. Tổng kết (Conclusion)
Greedy Algorithm trên mảng là kỹ năng tinh giản mạnh mẽ nhất. Việc áp dụng đúng lúc đúng chỗ không chỉ tiết kiệm được năng lượng xử lý và bộ nhớ mà còn thể hiện được tư duy "điểm chạm tối ưu" của ứng viên qua những chuỗi tính toán rất linh hoạt.
