# 05 - Prefix Sum and Hashing

## 1. Giới thiệu về Prefix Sum và Hashing
Kỹ thuật kết hợp **Prefix Sum** (Tổng tiền tố) và **Hashing** (Băm) là một trong những mẫu thuật toán (algorithmic patterns) mạnh mẽ nhất để giải quyết các bài toán liên quan đến mảng con (subarray) liên tục. Bằng cách lưu trữ trạng thái của prefix sum vào một Hash Map, ta có thể tra cứu thông tin về các mảng con trước đó trong thời gian O(1), giúp giảm độ phức tạp từ O(N^2) xuống O(N).

## 2. Ý tưởng cốt lõi (Core Idea)
Với một mảng `A`, tổng của mảng con từ chỉ số `i` đến `j` (với `i <= j`) có thể được tính bằng:
`Sum(i, j) = PrefixSum(j) - PrefixSum(i - 1)`
Nếu chúng ta muốn tìm một mảng con có tổng bằng `K`, điều kiện trở thành:
`PrefixSum(j) - PrefixSum(i - 1) = K`  =>  `PrefixSum(i - 1) = PrefixSum(j) - K`
Thay vì duyệt mọi `i` cho mỗi `j`, ta lưu các giá trị `PrefixSum` đã tính vào Hash Map. Khi duyệt đến `j`, ta chỉ cần kiểm tra xem `PrefixSum(j) - K` có tồn tại trong Hash Map hay không.

## 3. Kết hợp Prefix Sum với Hash Map cho Subarray Problems
Việc sử dụng Hash Map cho phép theo dõi lịch sử của các giá trị Prefix Sum. Khóa (Key) của Hash Map thường là giá trị Prefix Sum (hoặc trạng thái biến đổi nào đó như modulo, bitmask), và giá trị (Value) có thể là:
- **Tần suất (Count):** Số lần giá trị Prefix Sum này đã xuất hiện. Dùng để đếm số lượng mảng con.
- **Chỉ số (Index):** Vị trí đầu tiên hoặc cuối cùng mà Prefix Sum này xuất hiện. Dùng để tính chiều dài của mảng con.

## 4. Lưu trữ trạng thái: (prefixSum, count)
Được sử dụng khi bài toán yêu cầu **đếm số lượng** các mảng con thỏa mãn điều kiện.
- **Base case:** Khởi tạo `Map.put(0, 1)` (một mảng con rỗng có tổng bằng 0 xuất hiện 1 lần trước khi bắt đầu).
- **Cập nhật:** Tăng số đếm của `prefixSum` hiện tại sau mỗi bước duyệt.

## 5. Lưu trữ trạng thái: (prefixSum, index)
Được sử dụng khi bài toán yêu cầu tìm **chiều dài tối đa/tối thiểu** của mảng con.
- **Base case:** Khởi tạo `Map.put(0, -1)` (tổng bằng 0 tại chỉ số ảo -1).
- **Cập nhật:** Chỉ thêm `prefixSum` vào Hash Map nếu nó chưa tồn tại để bảo toàn chỉ số sớm nhất (giúp chiều dài tối đa).

## 6. Giải quyết bài toán "Subarrays with sum K" trong O(N)
Bài toán kinh điển: Đếm số mảng con có tổng bằng `K`.
- Khởi tạo: `count = 0`, `curr_sum = 0`, `map = {0: 1}`.
- Duyệt qua mảng:
  - `curr_sum += num`
  - Nếu `map.containsKey(curr_sum - K)`: `count += map.get(curr_sum - K)`
  - Thêm/cập nhật `curr_sum` vào `map`.
- Độ phức tạp: Thời gian O(N), Không gian O(N).

## 7. Toán tử Modulo với Prefix Sum
Khi đối mặt với các bài toán liên quan đến tính chia hết (ví dụ: mảng con có tổng chia hết cho `K`), chúng ta áp dụng toán học Modulo:
`(PrefixSum(j) - PrefixSum(i - 1)) % K == 0`  =>  `PrefixSum(j) % K == PrefixSum(i - 1) % K`
Nghĩa là nếu tổng tiền tố tại hai chỉ số có cùng phần dư khi chia cho `K`, tổng của mảng con giữa chúng sẽ chia hết cho `K`.

## 8. Xử lý số âm trong Modulo Arithmetic
Trong C++ và Java, toán tử `%` với số âm có thể trả về số âm (ví dụ: `-1 % 5 = -1`).
Để thống nhất phần dư dương, ta dùng công thức:
`rem = (sum % K + K) % K`

## 9. State Hashing và Bitmasks với Prefix Sum
Với bài toán liên quan đến tính chẵn lẻ của tần suất (ví dụ: chuỗi palindrome), ta có thể sử dụng mảng bit (bitmask) làm Prefix Sum. Mỗi bit trong integer biểu diễn trạng thái chẵn/lẻ của một phần tử. XOR được dùng thay cho phép cộng/trừ thông thường.

## 10. Các mẫu thuật toán phổ biến (Common Patterns)
1. **Sum == K:** Map lưu `(sum, count)` hoặc `(sum, index)`.
2. **Sum divisible by K:** Map lưu `(sum % K, count)`.
3. **Equal number of 0s and 1s:** Chuyển `0` thành `-1`, đưa về bài toán Sum == 0.
4. **Palindrome mask:** Map lưu `(bitmask, index)`.

## 11. Phân tích độ phức tạp (Complexity Analysis)
- **Time Complexity:** O(N) - duyệt qua mảng một lần, mỗi phép tra cứu trong Hash Map mất O(1) trung bình.
- **Space Complexity:** O(N) - trong trường hợp xấu nhất, mỗi phần tử sinh ra một prefix sum duy nhất (hoặc O(K) cho bài toán modulo K).

## 12. Ứng dụng trong Cấu trúc dữ liệu khác
- **Cây (Trees):** Tìm đường đi có tổng bằng `K` (Path Sum III). Hash Map được pass theo DFS/Backtracking (cần loại bỏ trạng thái - remove - khi quay lui).
- **Ma trận (Matrices):** Nén mảng 2D thành 1D rồi áp dụng Prefix Sum + Hashing để tìm submatrix (Submatrix Sum).

## 13. Ưu điểm và nhược điểm
- **Ưu điểm:** Giải quyết các mảng con chứa cả số âm; dễ hiểu khi đã nắm bắt ý tưởng; cực kì nhanh (O(N)).
- **Nhược điểm:** Tốn không gian bộ nhớ O(N); worst-case tra cứu Hash Map có thể O(N) nếu collision nhiều.

## 14. So sánh với Sliding Window
- **Sliding Window:** Chỉ dùng được khi mảng **không có số âm** (tổng đơn điệu tăng). Dùng hai con trỏ. Không gian O(1).
- **Prefix Sum + Hashing:** Xử lý được **mảng có số âm**. Không gian O(N).

## 15. Khi nào sử dụng Prefix Sum + Hashing?
- Yêu cầu mảng con liên tục (contiguous subarray).
- Có số âm.
- Tìm tổng, hiệu, độ dài, hoặc tính chia hết.

## 16. Các biến thể nâng cao
- Hash Map cho nhiều mảng cùng lúc.
- Kết hợp Hash Map và Binary Search trên một cấu trúc dữ liệu tổng hợp.

## 17. Cách tiếp cận bài toán (Problem-Solving Approach)
1. Đọc kĩ yêu cầu để xác định Key cho Hash Map (Sum, Modulo, Bitmask, Diff).
2. Xác định Value của Hash Map (Count, Min Index, Max Index).
3. Đừng quên thiết lập Base Case (`map.put(0, 1)` hoặc `map.put(0, -1)`).
4. Duyệt và tra cứu trước khi thêm phần tử hiện tại vào Map để tránh mảng rỗng (tùy bài toán).

## 18. Hướng dẫn gỡ lỗi (Debugging Guide)
- In ra bảng `prefixSum` và nội dung Hash Map tại mỗi bước.
- Kiểm tra lại công thức xử lý số âm.
- Đảm bảo logic tra cứu `K` chính xác (`prefixSum - K`).

## 19. Tối ưu hóa không gian bộ nhớ
Đối với bài toán Modulo K, Hash Map có thể thay bằng Array có kích thước `K` để tra cứu O(1) chắc chắn và tối ưu bộ nhớ.

## 20. 20 lỗi thường gặp (Common Bugs)
1. Quên thêm base case `map.put(0, 1)` cho đếm số lượng.
2. Quên thêm base case `map.put(0, -1)` cho tìm khoảng cách.
3. Không xử lý modulo cho số âm bằng `(sum % K + K) % K`.
4. Tràn số nguyên (Integer Overflow) khi tính `prefixSum` của mảng lớn (cần dùng `long`).
5. Tra cứu nhầm điều kiện (dùng `K - prefixSum` thay vì `prefixSum - K`).
6. Trong bài Path Sum trên Tree, quên xóa prefix sum khỏi Hash Map khi backtrack.
7. Áp dụng Sliding Window cho mảng chứa số âm thay vì dùng Prefix Sum + Hashing.
8. Update Hash Map trước khi kiểm tra (có thể gây sai nếu mảng con độ dài tối thiểu phải >= 1 và K=0).
9. Sử dụng kiểu `int` cho giá trị `count` khi tổng số lượng subarray có thể lên tới O(N^2) (cần `long` cho result).
10. Bài toán yêu cầu mảng con dài nhất nhưng lại đi ghi đè index trong Hash Map (lẽ ra chỉ add nếu chưa tồn tại).
11. Bài toán yêu cầu mảng con ngắn nhất nhưng không ghi đè index mới nhất trong Hash Map.
12. Xử lý modulo 0 dẫn đến lỗi chia cho 0.
13. Chuyển đổi 0 thành -1 sai cách trong bài toán cân bằng 0 và 1.
14. Nhầm lẫn giữa bitwise AND và XOR khi dùng bitmask (ví dụ: dùng `mask +=` thay vì `mask ^=`).
15. Không khởi tạo lại biến `sum` thành 0 cho nhiều test cases.
16. Dùng `HashMap` trong C++ bị timeout, cần dùng `unordered_map` kèm custom hash để tránh TLE.
17. Index tính độ dài sai: dùng `i - map.get(...) + 1` thay vì `i - map.get(...)`.
18. Quên break / return khi đã tìm thấy thỏa mãn điều kiện (nếu bài toán chỉ yêu cầu tìm "một" mảng con).
19. Gán nhầm biến trong vòng lặp lồng nhau (nếu giải naive array 2D).
20. Đếm cả các mảng con rỗng nếu logic loop không xử lí kỹ.

## 21. 30 trường hợp biên (Edge Cases)
1. Mảng rỗng.
2. Mảng chỉ có 1 phần tử.
3. Tất cả các phần tử đều là số 0.
4. Các phần tử đều âm.
5. K = 0.
6. K âm.
7. K cực kì lớn (Vượt quá tổng các phần tử).
8. Tổng của toàn bộ mảng bằng K.
9. Tổng tiền tố gặp K ngay ở phần tử đầu tiên.
10. Mảng đan xen số âm và số dương cân bằng nhau (vd: 1, -1, 1, -1).
11. Số lượng mảng con vượt quá `Integer.MAX_VALUE`.
12. Tổng các phần tử vượt quá `Integer.MAX_VALUE`.
13. Chia hết cho K nhưng K âm.
14. Kích thước mảng con yêu cầu tối thiểu là 2 (Continuous Subarray Sum).
15. K = 1 (mọi số đều chia hết cho 1).
16. Prefix Sum không bao giờ đạt K.
17. Giá trị chênh lệch (diff) giữa số lượng 2 loại phần tử luôn bằng 0.
18. Các mảng chứa số nguyên rất lớn yêu cầu dùng `BigInteger` (hiếm gặp nhưng có).
19. Trường hợp Hash Collision quá nhiều (nếu có chuỗi dữ liệu bị tấn công trong C++).
20. K là số nguyên tố lớn.
21. Bài toán Bitmask: 26 chữ cái chẵn lẻ -> mảng rỗng thì mask = 0 (luôn hợp lệ).
22. Trạng thái không xuất hiện lại lần nào trong mảng.
23. Gặp lại trạng thái liên tục tại các chỉ số kề nhau.
24. Prefix Sum chạm đáy (số lượng âm cực lớn) rồi tăng trở lại.
25. Mảng con nằm ở phần cuối của mảng đầu vào.
26. Mảng con thỏa mãn bắt đầu từ index 1 (cần trừ đi phần tử index 0 chuẩn xác).
27. Đối với bài Path Sum trên cây: Cây bị lệch hoàn toàn (như danh sách liên kết).
28. Đối với bài Path Sum: Cây có 1 node duy nhất và giá trị node bằng K.
29. Cấu hình modulo: K lớn hơn tổng của toàn mảng.
30. Yêu cầu tính chính xác không gian O(1) phụ nhưng bài giải dùng Hashing tốn O(N) (hỏi vặn).

## 22. 20 câu hỏi phỏng vấn (Interview Questions)
1. Tại sao Hash Map lại tối ưu hơn so với hai vòng lặp lồng nhau cho bài toán tổng mảng con?
2. Khi nào thì dùng Hash Map để lưu `index`, khi nào lưu `count`?
3. Sự khác biệt giữa `Sliding Window` và `Prefix Sum + Hashing`?
4. Tại sao cần `map.put(0, 1)` hoặc `map.put(0, -1)` ở đầu bài? Nếu không có thì sao?
5. Xử lý phần dư của số âm thế nào cho đúng trong Java/C++?
6. Bạn có thể tối ưu không gian thuật toán khi chia hết cho K không? (Dùng mảng size K).
7. Làm sao để áp dụng kĩ thuật này để đếm số mảng con có số lượng 0 và 1 bằng nhau?
8. Tại sao bài toán "Subarray Sum Equals K" lại không thể giải bằng Sliding Window? (Vì có số âm).
9. Nếu mảng hoàn toàn là số dương, bạn sẽ dùng Sliding Window hay Hash Map? (Sliding Window vì không gian O(1)).
10. Áp dụng thuật toán này trên Cây Nhị Phân (Binary Tree) thế nào?
11. Giải bài toán mảng con có tổng chia hết cho K như thế nào?
12. Có thể tìm số lượng Submatrix có tổng K trong ma trận 2D bằng cách này không?
13. Làm sao tìm mảng con chẵn lẻ xen kẽ dài nhất bằng Prefix Sum?
14. Bitmask hoạt động ra sao trong việc theo dõi chẵn lẻ của tần suất ký tự?
15. Khi `K = 0`, thuật toán đếm số mảng con tổng K có gì thay đổi không?
16. Nhược điểm về bộ nhớ của cách làm này là gì?
17. Xử lý "Continuous Subarray Sum" với điều kiện chiều dài tối thiểu >= 2 như thế nào?
18. Độ phức tạp không gian và thời gian khi áp dụng lên Ma trận 2D?
19. Giải pháp nếu ngôn ngữ không hỗ trợ Hash Map hiệu quả?
20. So sánh độ hiệu quả của mảng tần số với Hash Map khi Key bị giới hạn trong khoảng nhỏ.

## 23. Ứng dụng thực tế
- Các hệ thống phân tích dữ liệu log, nhận dạng mẫu tần suất.
- Xử lý chuỗi sinh học (DNA sequence) tìm chuỗi con có tỉ lệ nhất định.
- Tối ưu truy vấn dữ liệu tài chính (tìm khoảng thời gian có lợi nhuận / thua lỗ ròng bằng một mức cụ thể).

## 24. Tổng kết và Tự đánh giá
Prefix Sum kết hợp Hashing là vũ khí tối thượng cho lớp bài toán Subarray. Điểm quan trọng nhất là phải nhận diện được biểu thức cần tìm trong O(1) bằng cách biến đổi toán học `PrefixSum(i-1) = PrefixSum(j) - K`. Hãy luyện tập kĩ năng xử lý số âm, modulo và bitmask với Hashing để làm chủ hoàn toàn chuyên đề này.
