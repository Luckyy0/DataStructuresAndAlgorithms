# Advanced Greedy and Math

Thuật toán tham lam (Greedy) ở mức độ nâng cao thường không đứng một mình mà kết hợp chặt chẽ với các cấu trúc dữ liệu khác (như Priority Queue, Segment Tree) hoặc các tính chất toán học (Mathematics) phức tạp. Việc chứng minh tính tối ưu của các thuật toán Greedy này thường khó hơn nhiều so với các bài toán cơ bản.

Dưới đây là cấu trúc 24 phần chi tiết về **Advanced Greedy and Math**.

---

## 1. Giới thiệu (Introduction)
Advanced Greedy tập trung vào việc giải quyết các bài toán tối ưu hóa mà ở đó, các chiến lược tham lam cơ bản (sắp xếp rồi chọn) không đủ để giải quyết hoặc không đạt được độ phức tạp thời gian mong muốn. Nó thường yêu cầu cấu trúc dữ liệu bổ sung để duy trì trạng thái hiện tại một cách hiệu quả, hoặc cần những quan sát toán học sâu sắc để quyết định bước đi tham lam.

## 2. Triết lý cốt lõi (Core Philosophy)
Triết lý của Greedy là "lựa chọn tốt nhất ở hiện tại sẽ dẫn đến kết quả tốt nhất cục bộ, và từ đó dẫn đến kết quả tốt nhất toàn cục". Tuy nhiên, trong Advanced Greedy, "lựa chọn tốt nhất" có thể thay đổi theo thời gian và cần được cập nhật liên tục (đó là lý do cần Priority Queue) hoặc dựa trên các quy luật toán học tĩnh (Mathematics).

## 3. Greedy with Priority Queues / Heaps
Sự kết hợp phổ biến nhất trong Advanced Greedy. Khi chúng ta duyệt qua không gian trạng thái, chúng ta có thể "hối hận" (regret) về những lựa chọn tham lam trước đó. 
- **Regret Greedy**: Thêm phần tử vào Heap. Nếu gặp trạng thái vi phạm điều kiện, ta loại bỏ phần tử "tồi nhất" đã từng chọn từ Heap để khôi phục trạng thái hợp lệ nhưng với chi phí tối ưu hơn.
- Ví dụ: Bài toán lập lịch (Task Scheduling), Course Schedule III.

## 4. Greedy with Mathematics (Digit Manipulation & Combinatorics)
Đôi khi chiến lược Greedy yêu cầu kiến thức toán học, đặc biệt là với các thao tác trên chữ số (Digit Manipulation), số học (Arithmetic), hoặc hình học (Geometry).
- **Digit Manipulation**: Việc hoán đổi chữ số để được số lớn nhất (Maximum Swap). Ta cần theo dõi vị trí xuất hiện cuối cùng của các chữ số từ 0 đến 9.
- Bất đẳng thức, logic toán học để chứng minh việc sắp xếp theo một tiêu chí cụ thể luôn cho kết quả tốt hơn.

## 5. Proving Optimality in Complex Scenarios (Chứng minh tính tối ưu)
Việc chứng minh Greedy nâng cao tối ưu thường dựa trên:
1. **Exchange Argument (Lập luận hoán đổi)**: Giả sử có một giải pháp tối ưu khác không theo Greedy. Ta chứng minh rằng nếu hoán đổi các phần tử để nó giống với cấu trúc của Greedy, kết quả sẽ không tệ hơn.
2. **Greedy Stays Ahead**: Tại mỗi bước, lựa chọn của Greedy luôn tốt hơn hoặc bằng (theo một tiêu chí nào đó) so với bất kỳ giải pháp nào khác.

## 6. Lập luận Hoán đổi (Exchange Argument Technique)
Để sử dụng kịch bản này:
- Bước 1: Gọi thuật toán Greedy trả về kết quả $A$.
- Bước 2: Gọi kết quả tối ưu nhất là $O$. Giả sử $A \neq O$.
- Bước 3: Tìm ra sự khác biệt đầu tiên giữa $A$ và $O$.
- Bước 4: Hoán đổi các phần tử trong $O$ để khớp với $A$ và chứng minh điều này không làm thay đổi / làm tăng giá trị hàm mục tiêu.

## 7. Thuộc tính Lựa chọn Tham lam (Greedy Choice Property)
Trong bối cảnh nâng cao, lựa chọn tham lam đôi khi bị che khuất. Chẳng hạn, trong IPO (LeetCode 502), ta chỉ có thể chọn các dự án mà số vốn ban đầu (capital) thỏa mãn điều kiện. Greedy choice ở đây là: trong số các dự án khả thi, chọn dự án mang lại lợi nhuận cao nhất (cần Max Heap).

## 8. Cấu trúc con tối ưu (Optimal Substructure)
Mặc dù là Greedy, bài toán vẫn phải thỏa mãn tính cấu trúc con tối ưu, nghĩa là giải pháp tối ưu cho toàn bộ bài toán chứa đựng giải pháp tối ưu cho bài toán con. Tuy nhiên, khác với DP, ta không cần phải thử tất cả các bài toán con.

## 9. Greedy kết hợp Sweeping Line (Đường quét)
Sweeping Line thường dùng trong hình học hoặc các bài toán khoảng (Intervals) thời gian. Kết hợp với Greedy, ta duyệt các sự kiện theo thời gian, đồng thời duy trì trạng thái của các đoạn (Intervals) hoạt động, để tối đa hóa số lượng điểm bao phủ hoặc giảm thiểu số nhóm.

## 10. Greedy kết hợp Two Pointers
Hai con trỏ hỗ trợ Greedy bằng cách cung cấp cơ chế duyệt tối ưu $\mathcal{O}(N)$. Việc di chuyển con trỏ hoàn toàn phụ thuộc vào một quyết định tham lam (ví dụ: gỡ bỏ phần tử nhỏ hơn trong bài toán Trapping Rain Water hoặc Container With Most Water).

## 11. Ưu điểm của Advanced Greedy
- Độ phức tạp thời gian tối ưu, thường là $\mathcal{O}(N \log N)$ (với Heap/Sorting) hoặc $\mathcal{O}(N)$.
- Không tốn quá nhiều bộ nhớ so với Quy hoạch động (Dynamic Programming).
- Trực quan về mặt ý tưởng (sau khi đã chứng minh được tính đúng đắn).

## 12. Nhược điểm và Thách thức
- **Rất khó chứng minh tính đúng đắn**. Việc cảm nhận (intuition) một thuật toán Greedy có tối ưu hay không có thể dẫn đến sai lầm tốn kém (Wrong Answer).
- Khó nghĩ ra tiêu chí (criteria) để sắp xếp hoặc duy trì trong cấu trúc dữ liệu.
- Phụ thuộc mạnh vào việc nhận diện mẫu bài (Pattern recognition).

## 13. Hướng dẫn cài đặt (Implementation Guidelines)
1. Xác định rõ **tiêu chí sắp xếp** (Greedy criterion).
2. Viết Custom Comparator cẩn thận, đặc biệt trong các bài toán cần so sánh số lớn (tránh tràn số).
3. Đảm bảo cấu trúc dữ liệu bổ trợ (như PriorityQueue) được duy trì đúng.
4. Quản lý biến trạng thái (current capital, current time) chuẩn xác trong vòng lặp.

## 14. Độ phức tạp (Complexity)
- **Thời gian (Time)**: Đa số là $\mathcal{O}(N \log N)$ do sắp xếp mảng đầu vào hoặc thao tác với Priority Queue (với $N$ lần push/pop).
- **Không gian (Space)**: $\mathcal{O}(N)$ hoặc $\mathcal{O}(K)$ để lưu trữ dữ liệu trong Priority Queue, hoặc cấu trúc trợ giúp.

## 15. So sánh với Quy hoạch động (Dynamic Programming)
- **DP**: Duyệt qua mọi trạng thái có thể, không bỏ sót trường hợp nào (Safe but slow).
- **Greedy**: Đưa ra quyết định "một đi không trở lại" dựa trên heuristic cục bộ (Fast but risky). Advanced Greedy có thể thay thế DP trong một số bài toán cực đoan có constraints rất lớn ($N = 10^5$).

## 16. So sánh với Đồ thị (Graph Algorithms)
Một số thuật toán trên đồ thị như Dijkstra, Prim, Kruskal bản chất chính là Advanced Greedy. Chúng kết hợp Priority Queue để quyết định cạnh/đỉnh tối ưu tiếp theo cần duyệt qua.

## 17. Mẫu thiết kế (Design Patterns)
- **Sort + Iterate + Heap**: Sắp xếp đầu vào theo một điều kiện $X$. Khi duyệt, đẩy phần tử vào Heap. Nếu vi phạm, pop phần tử tồi nhất ra.
- **Two Heaps**: Duy trì Min-Heap và Max-Heap để cân bằng dữ liệu hoặc chọn lọc. (Dùng nhiều trong Data Stream Median, IPO).
- **Bucket / Frequency Map + Greedy**: Dùng mảng đếm tần suất để phục vụ thuật toán tham lam. (Task Scheduler).

## 18. Ứng dụng thực tế (Real-world Applications)
- **Hệ điều hành**: Lập lịch CPU (Shortest Job First).
- **Tài chính (Finance)**: Thuật toán giao dịch cao tần (HFT) để khớp lệnh.
- **Mạng máy tính**: Thuật toán định tuyến (Routing).
- **Nén dữ liệu**: Huffman Coding.

## 19. Cách nhận dạng (How to recognize)
- Đầu vào có giới hạn lớn ($N > 10^4$), loại trừ khả năng $\mathcal{O}(N^2)$ của DP.
- Yêu cầu "Tối đa", "Tối thiểu" chi phí/lợi nhuận.
- Các thao tác hoặc phần tử dường như độc lập (có thể sắp xếp hoặc xử lý cục bộ).
- Có thể "Undo" lại một lựa chọn kém (Gợi ý dùng Priority Queue).

## 20. Thực hành tốt nhất (Best Practices)
- Luôn thử nghiệm với các test cases nhỏ để xem chiến lược Greedy có phá vỡ tính tối ưu không.
- Đối với bài toán interval (khoảng thời gian), luôn vẽ trục thời gian và đánh dấu sự kiện để quan sát thuật toán Sweeping Line.
- Nếu DP bị TLE/MLE, hãy nghi ngờ có một thuật toán Greedy tinh tế hơn.

---

## 21. 30 Edge Cases (Trường hợp biên)
1. Mảng đầu vào rỗng.
2. Mảng chỉ có 1 phần tử.
3. Tất cả phần tử đều bằng nhau.
4. Mảng đã được sắp xếp sẵn (tăng hoặc giảm).
5. Priority Queue rỗng khi cố gắng lấy giá trị (`peek`/`poll`).
6. Tràn số nguyên (Integer Overflow) khi cộng dồn các giá trị lớn.
7. Các khoảng thời gian (Intervals) lồng nhau hoàn toàn.
8. Các khoảng thời gian chỉ giao nhau ở các điểm biên.
9. Giá trị âm và dương xen kẽ khiến hàm mục tiêu dao động.
10. Tổng tài nguyên nhỏ hơn yêu cầu tối thiểu của một nhiệm vụ.
11. Hai đối tượng có cùng tiêu chí so sánh (ví dụ: Profit bằng nhau, cần tiêu chí phụ là Cost).
12. Có phần tử trọng số / độ ưu tiên là 0.
13. Tần suất của một phần tử bằng tổng của tất cả các phần tử khác.
14. Chỉ có 2 giá trị độc lập xen kẽ liên tục.
15. Vượt quá giới hạn của mảng lưu đếm tần suất.
16. Đầu vào có mảng chứa các đối tượng có cùng Profit nhưng Cost rất lớn.
17. Sự thay đổi đột ngột lớn tại điểm cuối mảng (Spike at the end).
18. Test case toàn số 0.
19. Mảng các chuỗi cần sắp xếp nối chuỗi (ví dụ: Largest Number, "3" và "30").
20. Bài toán hoán đổi chữ số nhưng không có chữ số lớn hơn ở phía sau (đã đạt trạng thái max).
21. Nhiều chữ số lớn nhất xuất hiện trùng lặp trong bài hoán đổi (cần đổi với chữ số cuối cùng để tối ưu).
22. Các Worker trong bài Min Cost to Hire K Workers có Wage/Quality chênh lệch cực lớn.
23. Gặp vòng lặp vô hạn nếu logic cập nhật state không đảm bảo tăng dần thời gian.
24. Sử dụng Double để so sánh tỷ lệ trong Java bị lỗi sai số dấu phẩy động.
25. Tham số thời gian bắt đầu lớn hơn cả chuỗi sự kiện.
26. Không thể lập lịch cho bất kỳ sự kiện nào.
27. Đang chọn tham lam nhưng Heap đẩy phần tử không đúng kỳ vọng do viết sai custom Comparator.
28. K cần lớn hơn số phần tử hợp lệ trong tập (Out of Bound).
29. Quên clear Heap hoặc Reset Data structures khi chạy nhiều test case.
30. Yêu cầu tính Modulo $10^9+7$ ở mỗi thao tác Greedy lớn.

---

## 22. 20 Common Bugs and Pitfalls (Lỗi phổ biến)
1. Dùng `Arrays.sort()` cho tập dữ liệu chứa tỷ lệ (Double) mà không cẩn thận với NaN hoặc `0.0`.
2. Không cập nhật biến tổng / thời gian cục bộ đúng vòng lặp.
3. Thêm điều kiện dừng vòng lặp sớm làm lỡ mất các test case lớn ở cuối.
4. `poll()` phần tử khỏi Heap nhưng quên cập nhật lại tổng (`Sum -= polled.value`).
5. Sắp xếp sai mục đích: sắp xếp mảng Interval theo Start Time thay vì End Time ở bài đếm số Interval tối đa.
6. Lạm dụng Greedy: sử dụng Greedy cho bài toán thực sự yêu cầu DP (ví dụ: Coin Change với hệ tiền bất kỳ không chuẩn).
7. `PriorityQueue` lưu sai kiểu dữ liệu (ví dụ lưu class thay vì primitive) dẫn đến sinh ra hàng nghìn Objects gây GC Pause.
8. So sánh chuỗi bằng toán tử `<` thay vì `compareTo`.
9. Viết custom `Comparator` trả về giá trị kiểu `int` cho việc hiệu của 2 số `double`, dẫn đến sai số (nên dùng `Double.compare()`).
10. `Integer Overflow`: Khi tỷ lệ nhân ngược lại để so sánh tỷ lệ (ví dụ $a/b < c/d \Rightarrow a*d < b*c$), phép nhân tràn `Integer.MAX_VALUE`. Dùng `long` thay thế.
11. Quên `pop` khi kích thước của Heap vượt quá một giới hạn $K$.
12. Đẩy phần tử vào mảng đếm tần suất mà nhầm lẫn giữa giá trị (`value`) và vị trí (`index`).
13. Duyệt Sweeping Line nhưng không xử lý kịp thời các sự kiện có cùng vị trí/thời gian. Cần ưu tiên sự kiện `End` trước `Start` hoặc ngược lại tùy logic.
14. Không clone mảng đầu vào khi phải thao tác sắp xếp, phá hỏng dữ liệu gốc (nếu đề yêu cầu).
15. Không có `Math.max` hoặc `Math.min` để kẹp chặn (clamp) các giá trị sau phép toán.
16. Dùng `Math.abs` với `Integer.MIN_VALUE` dẫn đến tràn số.
17. Hiểu lầm tiêu chí Greedy (ví dụ: Tham lam lấy công việc có lợi nhuận cao nhất nhưng thời gian chạy quá dài).
18. Không xem xét số lượng tài nguyên thực sự cần để phân bổ (chỉ xem Profit).
19. Không test đủ sâu các trường hợp biên.
20. `NullPointerException` khi Priority Queue rỗng.

---

## 23. 20 Interview Questions (Câu hỏi phỏng vấn)
1. Tại sao bài toán Fractional Knapsack giải được bằng Greedy nhưng 0/1 Knapsack thì không?
2. Hãy chứng minh thuật toán của bạn luôn trả về kết quả tối ưu (Dùng Exchange Argument).
3. Độ phức tạp của thuật toán nếu không dùng Priority Queue mà dùng Array là gì?
4. Nếu thay Priority Queue bằng TreeMap thì sao? Khi nào nên dùng TreeMap thay cho Heap?
5. Nếu dữ liệu stream vào không ngừng, thuật toán Greedy của bạn có điều chỉnh để chạy Online được không?
6. PriorityQueue trong Java được implement như thế nào? Tại sao offer và poll mất $O(\log N)$?
7. Giải thích bài toán "Task Scheduler" và lý do sử dụng mảng đếm (Frequency Array) lại tối ưu hơn PQ trong một số trường hợp.
8. Có thể giải bài Course Schedule III bằng Quy hoạch động không? Tại sao dùng Greedy + Max Heap lại tốt hơn?
9. Thuộc tính Optimal Substructure và Greedy Choice Property liên kết với nhau như thế nào?
10. Khi nào kĩ thuật Regret Greedy sẽ bị thất bại?
11. Bạn xử lý như thế nào nếu 2 Intervals hoàn toàn giống hệt nhau về Start và End time?
12. Sweep Line (Đường quét) là gì? Khi nào bạn biết phải dùng Sweep Line thay vì quy hoạch động?
13. Giải bài toán Wiggle Subsequence. Tại sao Greedy approach (tìm local extrema) lại mang lại dãy dài nhất?
14. Khi yêu cầu là hoán đổi chữ số để được giá trị lớn nhất (Maximum Swap), thuật toán Greedy tốt nhất của bạn có độ phức tạp thời gian là bao nhiêu?
15. Tại sao trong Minimum Cost to Hire K Workers, chúng ta phải sắp xếp theo tỷ lệ Wage/Quality thay vì Quality?
16. Mô tả một kịch bản mà bạn nghĩ rằng Greedy tối ưu nhưng thực ra lại sai.
17. Bài toán Advantage Shuffle có phải là một dạng của thuật toán Greedy không?
18. Làm thế nào để giải quyết việc so sánh số nguyên lớn (chống tràn số) khi custom Comparator?
19. Giải thuật Greedy có luôn là giải thuật xấp xỉ tốt nhất cho các NP-hard problem không?
20. Bạn có thể tối ưu bộ nhớ $O(N)$ xuống $O(1)$ cho thuật toán này không?

---

## 24. Tổng kết (Summary)
Advanced Greedy và Math là những công cụ rất mạnh nhưng cũng rất nguy hiểm nếu không nắm chắc cốt lõi chứng minh tính tối ưu. Điểm mấu chốt là nhận diện đúng lúc nào cần kết hợp với **Priority Queue** (để thực hiện các bước "regret"), lúc nào cần **Sort + Two Pointers**, và cẩn trọng tối đa với các **trường hợp tràn số hoặc sai số phẩy động**. Nắm vững Exchange Argument sẽ giúp bạn tự tin vượt qua mọi buổi phỏng vấn khi được yêu cầu "Tại sao thuật toán của bạn lại đúng?".
