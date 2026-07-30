# 03 - Bit Masking

## 1. Khái niệm cơ bản (Introduction)
**Bit Masking** (Mặt nạ bit) là một kỹ thuật trong đó một con số nguyên (integer) được sử dụng để đại diện cho một tập hợp (set), trạng thái (state), hoặc một chuỗi các giá trị Boolean (đúng/sai). Mỗi bit của con số nguyên tương ứng với trạng thái có/không của một phần tử trong tập hợp. Ví dụ: với tập hợp $\{A, B, C\}$, số nhị phân `101` (5 trong hệ thập phân) có thể đại diện cho tập con $\{A, C\}$. Kỹ thuật này cho phép thực hiện các thao tác trên tập hợp như hợp (union), giao (intersection), hiệu (difference) một cách cực kỳ nhanh chóng bằng các phép toán bit (Bitwise Operations) như AND, OR, XOR.

## 2. Cách hoạt động (How it works)
- **Representing subsets using bits**: Một tập hợp có $N$ phần tử sẽ có $2^N$ tập con. Mỗi tập con có thể được đại diện bằng một số nguyên từ $0$ đến $2^N - 1$. Nếu bit thứ $i$ của số nguyên là 1, nghĩa là phần tử thứ $i$ có mặt trong tập con. Nếu bit thứ $i$ là 0, phần tử đó không có mặt.
- **Generating combinations/permutations with masks**: Bằng cách duyệt qua các số nguyên từ $0$ đến $2^N - 1$, ta có thể duyệt qua toàn bộ mọi cấu hình tập con (combinations) của tập hợp. Việc tạo hoán vị (permutations) kết hợp với bitmask cũng rất phổ biến trong các bài toán quy hoạch động (DP) trạng thái.
- **Applying bitmasks to state compression**: Trạng thái (state) của một hệ thống có $N$ công tắc bật/tắt (như các đỉnh đã đi qua trong thuật toán TSP, hay các bàn cờ đã điền) có thể được nén thành một số nguyên duy nhất (state compression). Điều này biến trạng thái đồ sộ thành một chỉ số mảng đơn giản (array index) để memoization.

## 3. Phân loại (Classification)
- **Subset Bitmasking**: Sinh tất cả các tập con, kiểm tra tính chất tập con.
- **State Compression (Nén trạng thái)**: Sử dụng trong Quy hoạch động (Bitmask DP), đồ thị (Bitmask BFS/DFS).
- **Flag Configuration**: Dùng để lưu trữ nhiều cờ Boolean (flags) trong một biến integer (thường thấy trong system programming, APIs).

## 4. Ưu điểm (Advantages)
- Tối ưu không gian cực tốt: $N$ biến Boolean được nén gọn thành một số nguyên ($O(1)$ thay vì $O(N)$ bộ nhớ).
- Thao tác rất nhanh: Thực thi song song (parallel logic) bằng một phép toán CPU (bitwise instruction) diễn ra trong $O(1)$ time.
- Dễ dàng làm chìa khóa (key) cho Hash Table hoặc chỉ số (index) cho Array trong memoization.

## 5. Nhược điểm (Disadvantages)
- Giới hạn bởi số lượng bit của kiểu dữ liệu: `int` 32-bit chỉ hỗ trợ tập hợp 32 phần tử, `long` 64-bit chỉ hỗ trợ 64 phần tử. Nếu $N > 64$, cần dùng cấu trúc dữ liệu khác như `BitSet`.
- Mã nguồn khó đọc: Các phép toán dịch bit (`<<`, `>>`) hoặc AND/OR (`&`, `|`) không trực quan với người mới, dễ gây nhầm lẫn.
- Khó debug: Xem giá trị số nguyên không cho thấy ngay trạng thái của các bit, cần phải chuyển đổi sang nhị phân.

## 6. Ứng dụng thực tế (Real-world applications)
- **Cấu hình hệ thống (System configuration/flags)**: Các quyền của tệp tin trong Linux (Read, Write, Execute = 4, 2, 1).
- **Networking**: Mạng máy tính sử dụng Subnet Mask để xác định Network ID và Host ID (IP addresses).
- **Game Engine**: Kiểm tra va chạm (collision masking), lưu trữ trạng thái nhân vật.
- **Thuật toán đồ thị**: Giải quyết bài toán Người chào hàng (Traveling Salesperson Problem - TSP), Matching.

## 7. Thời gian và Không gian (Time & Space Complexity)
- **Time Complexity**: Khởi tạo cấu hình và duyệt trạng thái thường là $O(2^N)$, hoặc $O(3^N)$ nếu duyệt qua các tập con của mọi tập con (submask enumeration). Thao tác bật/tắt/kiểm tra bit luôn là $O(1)$.
- **Space Complexity**: Sử dụng mảng để lưu kết quả cho từng state tốn không gian $O(2^N)$.

## 8. Các biến thể (Variations)
- **Submask Enumeration**: Kỹ thuật duyệt qua tất cả các tập con (submask) của một mask `M` cụ thể. Mã code: `for (int sub = M; sub > 0; sub = (sub - 1) & M)`.
- **Ternary Masking / Base-3 State Compression**: Dùng khi một phần tử có 3 trạng thái thay vì 2. Biểu diễn số bằng cơ số 3.

## 9. So sánh với các phương pháp khác (Comparison)
- **Bit Masking vs Array of Booleans**: Bitmask dùng ít bộ nhớ hơn, sao chép state rẻ hơn ($O(1)$ thay vì $O(N)$), so sánh state mất $O(1)$. Nhược điểm là chỉ dùng được với $N \le 64$.
- **Bit Masking vs Set/HashSet**: Bitmask loại bỏ chi phí hashing và cấp phát động, do đó nhanh hơn vô cùng nhiều khi phần tử là số nguyên nhỏ từ $0$ đến $63$.

## 10. Cấu trúc dữ liệu liên quan (Related Data Structures)
- **BitSet**: Lớp trong Java hoặc C++ hỗ trợ bitmasking không giới hạn kích thước (mảng các bit).
- **Array / HashTable**: Dùng để làm bảng quy hoạch động (DP table) lưu kết quả của từng mask.

## 11. Thuật toán liên quan (Related Algorithms)
- **Dynamic Programming with Bitmask (Bitmask DP)**: Rất mạnh để giải TSP, Hamiltonian Path, Job Assignment.
- **Backtracking**: Có thể dùng bitmask thay cho mảng `visited` để tiết kiệm chi phí undo/redo và tăng tốc chương trình.
- **Inclusion-Exclusion Principle (Nguyên lý bù trừ)**: Đếm số lượng, duyệt qua tất cả mask lẻ và chẵn.

## 12. 30 Edge Cases (Các trường hợp góc)
1. $N = 0$: Số tập con là 1 (chỉ có tập rỗng, bitmask = 0).
2. $N = 31$ trong Java/C++ với `int` (tràn bit dấu, cần cẩn thận phép `1 << 31`).
3. $N = 32$ với biến `int` 32-bit (Phép `1 << 32` trả về `1` thay vì `0` do undefined behavior / modulo shifting).
4. Mask có tất cả các bit là 1: `(1 << N) - 1`.
5. Kiểm tra subset rỗng.
6. Khi dịch bit âm (`-1 >> 1`), cần dùng logical shift (`>>>` trong Java).
7. Chỉ thao tác trên một bit không tồn tại (out of bounds).
8. Tính toán giao/hợp với mask bằng 0.
9. Đếm số bit 1 khi toàn bộ các bit đều là 1.
10. Tách bit 1 cuối cùng (LSB): `x & -x`, với $x = 0$ thì sao? (Cần check $x = 0$ trước).
11. Bỏ bit 1 cuối cùng: `x & (x - 1)`, với $x = 0$ trả về 0.
12. Mask sinh từ tập có phần tử trùng lặp (Duplicate elements).
13. Số lượng bit vượt quá 64 (sử dụng long cũng không đủ).
14. Phép toán quá mức ưu tiên toán tử (`==` có độ ưu tiên cao hơn `&`).
15. Khởi tạo mask bằng `1L << i` khi $i > 31$, nếu không có `L` thì bị tràn số `int` trước khi gán cho `long`.
16. Dùng `(mask & (1 << i)) > 0` thay vì `!= 0`, nếu bit thứ 31 được bật thì ra số âm, phép tính `> 0` bị sai.
17. Dịch phải (Right shift) `>>` bảo toàn dấu, dịch phải logic `>>>` không bảo toàn dấu.
18. Xóa bit 1 thấp nhất (Lowest Set Bit) bị lặp vô hạn nếu code sai vòng lặp while.
19. Submask enumeration với $M = 0$.
20. Phép toán NOT `~mask` trên biến `int` tạo ra hàng loạt bit 1 không mong muốn ở các bit cao, cần phải chặn (masking ra bit thừa bằng `& ((1<<N)-1)`).
21. Đổi dấu bit (Toggle) nhiều lần cùng một vị trí.
22. Tìm kiếm bit 1 cao nhất (Highest Set Bit) trên số 0.
23. Mask để đại diện cho set có thứ tự, bitmask mặc định không lưu thứ tự.
24. Mảng phần tử bị giới hạn (Empty string).
25. Chuyển đổi giữa bitmask và chuỗi bit (`Integer.toBinaryString`).
26. Kết quả của hàm hash lấy module bị âm, bitmask giải quyết bằng bitwise AND với kích thước hash table bậc 2.
27. Đếm bit 1 bằng thuật toán Kernighan (`x & (x-1)`) so với hàm có sẵn (`Integer.bitCount`).
28. Kiểm tra lũy thừa của 2: `(x & (x-1)) == 0`, nhưng cần check `x > 0`.
29. Cấu trúc mask chia theo đoạn (Bit-field): 10 bit cho giá trị A, 10 bit cho giá trị B, cần dịch chuẩn `(A << 10) | B`.
30. Tìm bit khác biệt giữa hai trạng thái: `A ^ B`.

## 13. 20 Common Bugs (Lỗi phổ biến)
1. Lỗi ưu tiên toán tử: `if (mask & 1 << i == 0)` bị sai do `==` thực hiện trước `&`. Cần sửa thành `if ((mask & (1 << i)) == 0)`.
2. Tràn số nguyên `int`: `1 << 31` hoặc lớn hơn với `int`. Dùng `1L << i`.
3. Kiểm tra bit: Dùng `> 0` thay vì `!= 0`. Khi bit cao nhất (sign bit) bằng 1, `mask & (1<<31)` trả về số âm, làm `> 0` sai.
4. Lầm tưởng `1 << N` là mask chứa N bit 1. Thực ra nó là $2^N$. Mask chứa N bit 1 là `(1 << N) - 1`.
5. Quên đóng khung bit sau phép NOT `~`. `~0` trong `int` là `0xFFFFFFFF` (chứa 32 bit 1), nếu N = 3, cần `~mask & 7`.
6. Tràn bit vòng (Shift modulo): Trong Java, dịch trái/phải với kiểu `int` sẽ thực hiện lấy modulo 32 với số dịch. `1 << 32` tương đương `1 << 0` (bằng 1).
7. Sử dụng sai Right shift `>>` và Unsigned Right shift `>>>`.
8. Lặp vô hạn khi đếm submask nếu không có lệnh trừ/giảm hợp lý.
9. Đặt sai vị trí dấu ngoặc khi trộn logic AND, OR, XOR.
10. Nhầm lẫn giữa bitwise AND `&` và logical AND `&&`.
11. Bật bit `mask += (1 << i)` mà không kiểm tra xem bit đã được bật chưa, gây nhớt bit lân cận. Nên dùng `mask |= (1 << i)`.
12. Xóa bit sai: `mask -= (1 << i)` khi bit đó đang là 0. Nên dùng `mask &= ~(1 << i)`.
13. Đảo bit sai: Dùng `~` trên toàn mask thay vì dùng `^ (1 << i)` để đảo 1 bit.
14. Sử dụng Bitmask với chỉ số 1-based (từ 1 đến N). Bitmask bắt đầu từ bit 0, dễ nhầm `1 << N` cho mảng kích thước N.
15. Không xử lý padding/leading zeros khi in chuỗi nhị phân (Binary string).
16. Nhầm lẫn về độ phức tạp khi dùng submask enumeration ($3^N$ chứ không phải $4^N$).
17. Áp dụng nhầm XOR thay vì OR để gộp tập hợp (chỉ gộp đúng khi 2 tập rời rạc).
18. Không ép kiểu (Cast) về kiểu dữ liệu lớn hơn khi cần `long` mask = `(long) a | ((long)b << 32)`.
19. Gắn mask là chỉ số âm trong mảng gây lỗi OutOfBounds.
20. Tràn số khi nhân hoặc cộng các giá trị bit được phân tách, do chưa mask cẩn thận.

## 14. 20 Interview Questions (Câu hỏi phỏng vấn)
1. Tại sao thao tác dịch bit lại được ưa chuộng hơn nhân/chia cho 2 trong lập trình hệ thống?
2. Hãy giải thích tại sao `x & (x - 1)` lại xóa đi bit 1 ở vị trí thấp nhất (LSB)?
3. Làm sao để kiểm tra một số có phải là lũy thừa của 2 bằng O(1) thao tác?
4. Đếm số lượng bit 1 trong một số nguyên? Thuật toán tối ưu nhất là gì?
5. Làm cách nào để lấy giá trị LSB (Least Significant Bit) của một số nguyên dương? (`x & -x`)
6. Sự khác biệt giữa `>>` và `>>>` trong Java là gì?
7. Giải thích thuật toán đổi chỗ (Swap) hai biến A và B không cần biến tạm dùng XOR. Tại sao ít được dùng thực tế?
8. Kỹ thuật duyệt qua tất cả tập con (submask) của một mask diễn ra trong độ phức tạp bao nhiêu?
9. "State compression" trong quy hoạch động có nghĩa là gì?
10. Nếu hệ thống có 100 cái cờ (flags), bạn sẽ dùng cấu trúc dữ liệu nào thay vì `int` hay `long`?
11. Làm thế nào để đóng gói tọa độ (x, y) trên ma trận 1000x1000 vào một số `int` duy nhất?
12. Làm sao để lật (Toggle) bit thứ i của mask?
13. Làm sao để tạo ra một chuỗi nhị phân chứa 5 bit 1 ở bên phải cùng?
14. Nếu có mảng có 1 triệu phần tử, mỗi phần tử xuất hiện 2 lần trừ 1 phần tử xuất hiện 1 lần. Làm sao tìm nó trong O(N) thời gian, O(1) không gian?
15. Sự khác biệt giữa XOR của tất cả số từ 1 đến N và ...?
16. Mọi thao tác bit có thật sự O(1) trên bất kỳ kiến trúc phần cứng nào không?
17. Cách chuyển chữ hoa thành chữ thường bằng bitwise manipulation trong ASCII?
18. Tại sao độ ưu tiên của bitwise operators trong C/Java lại thấp hơn phép toán so sánh `==`? (Yếu tố lịch sử B language).
19. Bằng cách nào dùng phép dịch bit và logic cơ bản để thực hiện phép cộng (Add without + operator)?
20. Bạn sẽ debug bitmask DP như thế nào khi mảng DP có tới $2^{20}$ trạng thái?

## 15. Mẹo và Best Practices (Tips)
- Luôn luôn sử dụng dấu ngoặc đơn `()` khi kết hợp bitwise operations với các biểu thức logic hay toán học, vd: `if ((mask & (1 << i)) != 0)`.
- Khi $N \ge 32$, bắt buộc sử dụng `1L << i` và kiểu `long` để tránh tràn số.
- Sử dụng hàm có sẵn trong ngôn ngữ như `Integer.bitCount(mask)` để lấy số lượng bit 1 (popcount) vừa nhanh vừa chính xác.
- Khi cần duyệt qua từng tập con của 1 tập $N$ phần tử: `for(int mask = 0; mask < (1 << N); mask++)`.
- Có thể dùng mảng hằng số (precomputed array) để lưu số bit 1 của mọi số từ $0$ đến $2^{16}-1$, giúp tăng tốc tra cứu popcount khi tự cài đặt.

## 16. Hướng dẫn gỡ lỗi (Debugging Guide)
- Viết một hàm in bitmask ra dạng nhị phân có đủ độ dài cố định (vd: `000101` thay vì `101`) để dễ hình dung, ví dụ `String.format("%32s", Integer.toBinaryString(mask)).replace(' ', '0')`.
- Đảm bảo kiểm tra đúng `long` hay `int` trong quá trình tính toán. Một `mask` được bật bit bằng `(1 << 35)` sẽ bị sai, phải là `(1L << 35)`.
- Giám sát độ ưu tiên toán tử nếu điều kiện `if` không nhảy vào như mong đợi.

## 17. Design Patterns liên quan
- **Bit Flags**: Cờ thuộc tính (Enum Sets trong Java được implement ngầm qua bitmask `RegularEnumSet`).
- **Flyweight**: State compression bằng bitmask giúp hạn chế số lượng đối tượng sinh ra.

## 18. Thư viện và Framework
- **Java**: `java.util.BitSet`, `EnumSet`, các phương thức static của lớp `Integer` (`bitCount`, `lowestOneBit`, `highestOneBit`, `numberOfLeadingZeros`).
- **C++**: `<bitset>`, `__builtin_popcount()`.
- **Python**: Các toán tử bit (`&`, `|`, `^`, `<<`, `>>`), kiểu số nguyên tự động mở rộng không giới hạn kích thước, thuộc tính `.bit_count()`.

## 19. Mở rộng (Scaling/Advanced topics)
- **Submask Enumeration (Duyệt tập con của tập con)**: Tổng chi phí vòng lặp chạy qua toàn bộ tập con của toàn bộ mask $N$ bit là $\sum_{k=0}^{N} \binom{N}{k} 2^k = 3^N$, thay vì $4^N$.
- **Profile DP (Quy hoạch động trên mặt cắt)**: Một kỹ thuật cao cấp trong đó trạng thái tại hàng $i$ của một lưới $N \times M$ được lưu trữ bằng bitmask, thường dùng để lát gạch domino.

## 20. Tài liệu tham khảo (References)
- CP-Algorithms: Bit manipulation.
- Codeforces Tutorials: Bitmask DP.
- Tác giả Hacker's Delight: Henry S. Warren (Sách chuyên sâu về Bitwise tricks).

## 21. Lịch sử và Nguồn gốc (History)
Bit masking có từ thời kỳ đầu của điện toán, khi không gian nhớ là một thứ cực kì xa xỉ (ví dụ trên các hệ máy tính băng từ hoặc bộ nhớ 16KB). Các kĩ sư buộc phải nén càng nhiều boolean cờ (flags) vào một từ (word) hay một byte càng tốt để duy trì sự sống của chương trình.

## 22. Các lỗi nhận thức (Misconceptions)
- Hiểu nhầm: "Thao tác bit luôn chạy nhanh hơn nhiều." Sự thật: Các Compiler hiện đại (JIT trong Java, GCC) thường tự động tối ưu hóa phép nhân/chia 2 thành shift, và modulo $2^k$ thành bitwise AND. Sự khác biệt tốc độ chỉ đáng kể khi logic cốt lõi thiết kế xoay quanh tính song song trên cấp độ bit (Bit parallelism).
- Dịch trái N lần tạo ra mask có N bit 1: SAI. Dịch trái 1 `<< N` tạo ra một số hạng duy nhất là $2^N$. Cần trừ 1 `(1 << N) - 1`.

## 23. Tương lai (Future Trends)
Dù bộ nhớ đã rẻ mạt, nhưng trong kỷ nguyên xử lý dữ liệu khổng lồ (Big Data, Bitmap Index trong Database) và Machine Learning (Quantization với low-precision), kỹ thuật làm việc trực tiếp trên tập hợp bit đang quay trở lại để giảm bottleneck bộ nhớ cache (CPU Cache lines).

## 24. Tổng kết (Conclusion)
Bit Masking (Mặt nạ bit) là một kỹ thuật mạnh mẽ, không thể thiếu trong balo của mọi lập trình viên. Nó tạo ra các giải pháp ngắn gọn, tiết kiệm không gian và hiệu suất tối ưu để giải quyết các bài toán duyệt cấu hình (subsets/combinations) cũng như là chìa khóa cơ bản nhất đưa ta vào cánh cửa Quy hoạch động nén trạng thái (State-compressed DP).
