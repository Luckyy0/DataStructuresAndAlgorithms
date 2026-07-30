# 04. Knapsack and Subset DP (Quy Hoạch Động Cái Túi và Tập Con)

## 1. Khái niệm cơ bản (Introduction)
Bài toán cái túi (Knapsack Problem) là một trong những bài toán kinh điển nhất trong kỹ thuật Quy hoạch động (Dynamic Programming - DP). Bài toán xoay quanh việc lựa chọn một tập con các vật phẩm từ một tập hợp cho trước, mỗi vật phẩm có một trọng lượng (weight) và giá trị (value) nhất định, sao cho tổng trọng lượng không vượt quá sức chứa tối đa của túi (capacity) và tổng giá trị đạt mức lớn nhất.

## 2. Các loại Knapsack (Types of Knapsack)
Có ba biến thể chính của bài toán Knapsack:
- **0/1 Knapsack:** Mỗi vật phẩm chỉ có thể được chọn tối đa một lần (chọn hoặc không chọn).
- **Unbounded Knapsack:** Mỗi vật phẩm có thể được chọn với số lượng vô hạn.
- **Bounded Knapsack:** Mỗi vật phẩm có một số lượng giới hạn nhất định (ví dụ vật phẩm i có tối đa $C_i$ cái).

## 3. Khái niệm Subset Sum (Subset Sum Concepts)
Subset Sum (Tổng tập con) là một hệ quả hoặc biến thể đơn giản hóa của 0/1 Knapsack. Bài toán yêu cầu xác định xem có tồn tại một tập con các phần tử trong mảng sao cho tổng của chúng đúng bằng một giá trị `Target` cho trước hay không.
- Thay vì tối đa hóa `value`, ta chỉ cần theo dõi tính "đạt được" (boolean `True/False`) của một mức tổng `Target`.

## 4. Bounded Knapsack
Bounded Knapsack nằm giữa 0/1 và Unbounded, trong đó số lượng mỗi món đồ bị giới hạn. Giải pháp thông thường là chuyển bài toán Bounded Knapsack thành 0/1 Knapsack thông qua kỹ thuật gộp đồ (Binary Splitting) để giảm số lượng vật phẩm.

## 5. Nhận diện mẫu bài toán Knapsack (Identifying Knapsack Patterns)
Các dấu hiệu đặc trưng của Knapsack Pattern:
1. Cho một tập hợp các đối tượng (mảng `nums`, đồng xu `coins`, chuỗi ký tự...).
2. Một giới hạn nhất định (Sức chứa `W`, Mục tiêu `Target`, `Amount`...).
3. Yêu cầu tính: Giá trị tối đa/tối thiểu (Max/Min Value), Tổng số cách (Number of ways), Khả năng đạt được (Boolean check).
4. Quyết định tuần tự cho mỗi vật phẩm (Bao gồm vật phẩm này hoặc bỏ qua nó).

## 6. Công thức truy hồi cơ bản (Basic Recurrence Relations)
- **0/1 Knapsack:** `dp[i][w] = max(dp[i-1][w], dp[i-1][w - weight[i]] + value[i])`
- **Unbounded Knapsack:** `dp[i][w] = max(dp[i-1][w], dp[i][w - weight[i]] + value[i])` (Chú ý sử dụng `dp[i]` thay vì `dp[i-1]` do có thể dùng lại vật phẩm i).
- **Subset Sum (Boolean):** `dp[w] = dp[w] || dp[w - weight[i]]`

## 7. Kỹ thuật tối ưu hóa không gian (Space Optimization Techniques)
Thay vì sử dụng mảng 2D `dp[N][W]`, ta có thể giảm xuống mảng 1D `dp[W]`:
- Với **0/1 Knapsack**, ta bắt buộc phải duyệt trọng lượng (capacity) từ `W` về `0` (duyệt ngược) để tránh việc sử dụng cùng một vật phẩm nhiều lần trong cùng một bước.
- Với **Unbounded Knapsack**, ta duyệt trọng lượng từ `0` đến `W` (duyệt thuận) để cho phép cộng dồn nhiều lần một vật phẩm.

## 8. Khởi tạo mảng DP (DP Array Initialization)
- **Bài toán Max Value:** `dp[0] = 0`, các phần tử khác khởi tạo `0` (hoặc âm vô cực tùy ngữ cảnh).
- **Bài toán Min Value:** `dp[0] = 0`, các phần tử khác khởi tạo `Infinity`.
- **Bài toán Number of Ways (đếm số cách):** `dp[0] = 1`, các phần tử khác `0`.
- **Bài toán Boolean Check:** `dp[0] = True`, các phần tử khác `False`.

## 9. Backtracking và khôi phục kết quả (Backtracking and Result Reconstruction)
Để biết được những đồ vật nào đã được chọn, ta duyệt ngược từ `dp[N][W]`. Nếu `dp[i][w] != dp[i-1][w]`, vật phẩm thứ `i` đã được chọn. Trừ đi giá trị trọng lượng hiện tại và tiếp tục truy vết. (Kỹ thuật này dễ dàng hơn trên mảng DP 2 chiều).

## 10. Các biến thể phổ biến (Common Variations)
- **Coin Change:** Thay vì có giới hạn 1 cái, ta có vô số đồng xu (Unbounded Knapsack) để tìm cách ghép nhỏ nhất hoặc số cách.
- **Partition Equal Subset Sum:** Tìm tập con có tổng bằng `Sum(array) / 2`.
- **Target Sum:** Gán dấu `+` và `-` vào các phần tử (chuyển về bài toán Subset Sum).

## 11. Đánh giá ưu nhược điểm (Pros and Cons)
- **Ưu điểm:** Phương pháp DP đảm bảo tìm ra nghiệm tối ưu toàn cục. Tối ưu không gian bằng mảng 1D rất hiệu quả.
- **Nhược điểm:** Độ phức tạp là Pseudo-polynomial. Nếu giá trị của sức chứa `W` rất lớn (ví dụ $10^9$), mảng DP không thể lưu trữ và cần dùng các kỹ thuật khác (Meet-in-the-Middle).

## 12. So sánh với các kỹ thuật khác (Comparison with other techniques)
- **Greedy:** Greedy chỉ tối ưu đối với Fractional Knapsack (Cho phép chia nhỏ đồ vật). 0/1 Knapsack bắt buộc dùng DP.
- **Backtracking:** Cây đệ quy sẽ có thời gian chạy là $O(2^N)$. DP dùng Memoization / Tabulation giảm xuống $O(N \times W)$.

## 13. Tính chất và Định lý (Properties and Theorems)
- Knapsack là một bài toán NP-Complete (NP-Hoàn chỉnh). Độ phức tạp của thuật toán DP không hoàn toàn là đa thức đối với kích thước dữ liệu đầu vào tính bằng bit.
- Có tính chất Optimal Substructure (Cấu trúc con tối ưu) và Overlapping Subproblems (Bài toán con gối nhau).

## 14. Phân tích độ phức tạp (Complexity Analysis)
- **Thời gian (Time Complexity):** $O(N \times W)$ cho cả 0/1 và Unbounded Knapsack, với $N$ là số lượng phần tử và $W$ là sức chứa.
- **Không gian (Space Complexity):** $O(W)$ nếu áp dụng kỹ thuật cuộn mảng (Array Rolling / 1D DP), và $O(N \times W)$ nếu dùng bảng DP 2 chiều nguyên gốc.

## 15. Ứng dụng thực tế (Real-world Applications)
- Quản lý bộ nhớ hệ điều hành, Cắt vật liệu (Cutting Stock).
- Lựa chọn danh mục đầu tư (Portfolio Selection) với ngân sách giới hạn.
- Đóng gói hàng hóa, quản lý chuỗi cung ứng.

## 16. Mẫu code cơ bản (Basic Code Templates)

### Template 0/1 Knapsack (Tối ưu mảng 1D)
```java
int[] dp = new int[W + 1];
for (int i = 0; i < N; i++) {
    for (int w = W; w >= weight[i]; w--) {
        dp[w] = Math.max(dp[w], dp[w - weight[i]] + value[i]);
    }
}
```

### Template Unbounded Knapsack (Tối ưu mảng 1D)
```java
int[] dp = new int[W + 1];
for (int i = 0; i < N; i++) {
    for (int w = weight[i]; w <= W; w++) {
        dp[w] = Math.max(dp[w], dp[w - weight[i]] + value[i]);
    }
}
```

## 17. Các bài toán kinh điển (Classic Problems)
- Knapsack truyền thống (0/1).
- Coin Change 1 & 2.
- Rod Cutting (Unbounded Knapsack).
- Target Sum.

## 18. Mẹo tối ưu hóa hiệu suất (Performance Optimization Tips)
- Thoát sớm (Early exit) nếu `Target` đã đạt được `True` (trong Subset Sum) bằng cách break khỏi vòng lặp trong, giảm thiểu số phép duyệt dư thừa.
- Sắp xếp (Sorting) các phần tử lớn trước đôi khi giúp giảm thời gian chạy nhánh cận trong đệ quy.

## 19. Các biến thể nhiều chiều (Multi-dimensional Variations)
- Đôi khi túi không chỉ giới hạn về trọng lượng mà còn về thể tích (ví dụ Ones and Zeroes - giới hạn số lượng số 0 và số 1). Khi đó ta sử dụng DP mảng 2 chiều (hoặc 3 chiều nếu không tối ưu bộ nhớ): `dp[zeros][ones]`.

## 20. Bài toán Knapsack trên Cây (Tree Knapsack Problem)
- Khi các vật phẩm có sự phụ thuộc lẫn nhau dưới dạng cây cấu trúc phân cấp (Phải lấy node cha mới được lấy node con), bài toán chuyển hóa thành Tree DP + Knapsack. Ở mỗi node, ta kết hợp (merge) mảng knapsack của các nhánh con.

## 21. 20 câu hỏi phỏng vấn thường gặp (20 Interview Questions)
1. Sự khác biệt giữa 0/1 Knapsack và Unbounded Knapsack về công thức truy hồi và cấu trúc vòng lặp là gì?
2. Tại sao tối ưu bộ nhớ trong 0/1 Knapsack lại yêu cầu duyệt vòng lặp ngược?
3. Làm thế nào để quy bài toán Target Sum về bài toán Subset Sum?
4. Fractional Knapsack có dùng DP để giải không? Tại sao?
5. Định nghĩa độ phức tạp Pseudo-polynomial time trong Knapsack Problem.
6. Làm thế nào để in ra các vật phẩm đã chọn trong bài toán Knapsack?
7. Sự khác nhau giữa Coin Change (tìm số xu ít nhất) và Knapsack (tìm Max Value) là gì?
8. Bài toán Subset Sum bằng cách nào giúp ta giải Partition Equal Subset Sum?
9. Nếu số lượng vật phẩm (N) lớn và `W` nhỏ, ta nên dùng cách gì?
10. Nếu `W` lớn (lên tới $10^9$) nhưng `N` rất nhỏ ($N \le 40$), làm sao để xử lý? (Gợi ý: Meet-in-the-Middle).
11. Bounded Knapsack có thể được quy về 0/1 Knapsack như thế nào? (Binary Splitting).
12. Có thể tồn tại trọng lượng (weight) âm trong bài toán Knapsack được không?
13. Độ phức tạp không gian của Multi-dimensional Knapsack (như bài Ones and Zeroes) là bao nhiêu?
14. Trong trường hợp nào DP cho bài toán Subset Sum sẽ chậm hơn Backtracking với Pruning?
15. Khởi tạo mảng DP cho Unbounded Knapsack ở bài toán "Coin Change - Số xu ít nhất" khác bài "Knapsack - Max Value" chỗ nào?
16. Cách đếm số lượng cách xếp đồ trong Knapsack (Number of ways)?
17. Nếu bài toán có thêm một ràng buộc "phải chọn đúng K vật phẩm", làm sao mở rộng trạng thái DP?
18. Tree Knapsack Problem là gì và được tiếp cận như thế nào?
19. Việc đảo ngược thứ tự hai vòng lặp (Capacity ra ngoài, Vật phẩm vào trong) trong Coin Change 2 ảnh hưởng gì? (Gợi ý: Trở thành Permutations thay vì Combinations).
20. Tại sao `Target Sum` cần kiểm tra `(sum + target) % 2 == 1` trước khi đưa vào DP?

## 22. 20 lỗi phổ biến (20 Common Bugs)
1. **Lỗi duyệt vòng lặp thuận trong 0/1 Knapsack** khiến một đồ vật bị cộng dồn vô hạn lần.
2. **Khởi tạo mảng sai:** Khởi tạo `0` thay vì `Infinity` khi bài toán yêu cầu tìm giá trị Min.
3. **Tràn số Integer (Overflow):** Gán `Integer.MAX_VALUE` rồi lấy nó cộng thêm 1 dẫn đến giá trị âm.
4. **Sai kích thước mảng:** Khai báo mảng `new int[W]` thay vì `new int[W + 1]`.
5. **Không kiểm tra số lẻ:** Quên kiểm tra trường hợp tổng mảng là lẻ trước khi chia đôi ở bài Partition Equal Subset Sum.
6. **Không sử dụng modulo:** Trong các bài "đếm số cách", quên `% 1000000007` dẫn đến kết quả sai.
7. **Tràn bộ nhớ:** Khai báo mảng `new int[10^9]` làm chương trình crash vì `W` quá lớn.
8. **Điều kiện chuyển trạng thái sai:** Chỉ dùng `dp[w-weight] + val` mà quên lấy hàm `Math.max()`.
9. **Bỏ quên số 0:** Không xem xét đầu vào mảng `nums` có số 0 (sẽ tăng gấp đôi số cách đếm trong Subset Sum).
10. **Tối ưu hóa không đáng có:** Áp dụng thuật toán sắp xếp `Arrays.sort()` vào mảng không cần thiết làm chậm code.
11. **Quên trả về phần tử cuối:** Return `max(dp)` thay vì `dp[W]` trong bài toán yêu cầu tối ưu cho đúng W.
12. **Mất trạng thái trước đó:** Trong DP 2D, không gán `dp[i][j] = dp[i-1][j]` khi đồ vật i không thể bỏ vừa vào túi.
13. **Đảo lộn chiều vòng lặp:** Ở một số bài toán (như Combination Sum IV), đổi thứ tự hai vòng lặp (Items - Target) biến đáp án từ Tổ hợp thành Chỉnh hợp.
14. **Bỏ qua phần tử 0 đồng:** Trong Coin Change, tham số `amount = 0` nhưng lại thiết kế DP trả về -1 thay vì 0.
15. **Xử lý số âm không tốt:** Không kiểm tra `Target` âm trong các bài có phép tính biến đổi.
16. **Sai lầm với đồ vật có $W = 0$:** Các đồ vật này không làm đầy túi nhưng lại cộng thêm Value, code bỏ qua chúng sẽ thiếu đáp án.
17. **Khởi tạo `dp[0] = 1` sai mục đích:** Đặt `dp[0] = 1` trong bài toán "Tìm Max Value" thay vì chỉ dùng cho bài "Number of ways".
18. **Tràn kiểu Int khi Sum quá lớn:** Phải sử dụng mảng kiểu `long[]` nếu số lượng tổ hợp lớn.
19. **Bỏ qua Edge Case mảng rỗng:** Dẫn đến Exception `IndexOutOfBounds`.
20. **Tính sai tổng sức chứa yêu cầu:** Chẳng hạn Last Stone Weight 2 cần sức chứa `Sum / 2` nhưng lại cấp phát mảng bằng `Sum`.

## 23. 30 trường hợp biên (30 Edge Cases)
1. Mảng đồ vật rỗng (Empty array).
2. Sức chứa của ba lô $W = 0$.
3. Mọi đồ vật đều có trọng lượng lớn hơn $W$.
4. Một hoặc nhiều đồ vật có trọng lượng $0$ và giá trị $0$.
5. Mảng đầu vào chỉ có đúng 1 đồ vật.
6. Sức chứa $W$ rất lớn, gấp nhiều lần tổng trọng lượng của tất cả phần tử (chỉ cần lấy tổng tất cả giá trị).
7. Mảng `nums` gồm các phần tử giống hệt nhau về mọi thông số.
8. Bài toán Subset Sum với tổng các phần tử đúng bằng $0$.
9. Target Sum có giá trị cần tìm lớn hơn tổng các phần tử trong mảng (luôn trả về 0 cách).
10. Yêu cầu tính tổng số cách nhưng target sum lẻ (ví dụ: Partition Equal Subset Sum mảng lẻ).
11. Bài toán Subset Sum với target = 0 (luôn có ít nhất 1 cách là tập rỗng).
12. Sức chứa $W$ là số âm (không hợp lệ, trả về mặc định).
13. Coin Change với các đồng xu có mệnh giá rất lớn so với `Amount`.
14. Coin Change 1 nhưng không có cấu hình đồng xu nào ghép vừa `Amount` (phải trả về -1).
15. Ones and Zeroes với $M = 0$ hoặc $N = 0$.
16. Profitable Schemes có `minProfit = 0` (Mọi cấu hình số người miễn trong giới hạn đều hợp lệ).
17. Mảng `costs` trong Minimum Cost For Tickets có giá vé 1 ngày lớn hơn vé 30 ngày.
18. Giá trị Target Sum biến thành số âm khổng lồ.
19. Mảng toàn số 0 trong bài toán Target Sum yêu cầu số lượng cấu hình rất lớn ( $2^K$ ).
20. Một đồ vật cực lớn (trọng lượng $W$) và có value tối đa.
21. Bài toán 2D Knapsack với một trong hai không gian chiều sức chứa là 0.
22. Trọng lượng các phần tử được biểu diễn bằng số thực Float (Cần chuẩn hóa / nhân $10^x$ về số nguyên trước khi DP).
23. Gặp tràn số nguyên (`Integer Overflows`) trong các truy vấn trung gian.
24. Quản lý modulo liên tục trong quá trình cộng mảng DP (Không đợi đến khi cộng xong mới modulo).
25. Mọi Value đều là âm trong bài toán Max Value (Lợi ích không có, nên không nhặt gì, max = 0).
26. Target Sum tạo ra biểu thức toán $(sum + target)$ bị âm.
27. Đếm số cấu hình với `target < 0`.
28. Một số bài toán yêu cầu chứa đầy túi hoàn toàn (Exact match), `dp` khởi tạo `Negative Infinity`.
29. Coin change có đồng xu có mệnh giá âm (không có trong đề chuẩn, nhưng là edge case lý thuyết).
30. Yêu cầu số món đồ không vượt quá K, nhưng mảng đồ vật ít hơn K phần tử.

## 24. Tài liệu tham khảo (References)
- *Introduction to Algorithms (CLRS)* - Chương về Dynamic Programming.
- *Competitive Programmer's Handbook* - Phần Knapsack Problems.
- Các bài tập luyện tập trên LeetCode, Codeforces.
- Các bài giảng về Optimization Techniques và Pattern Recognition.
