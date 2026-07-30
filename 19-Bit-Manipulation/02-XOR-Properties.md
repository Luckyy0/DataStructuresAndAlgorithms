# 02 - XOR Properties (Tính chất của phép XOR)

## 1. Mở đầu (Introduction)
Phép toán Bitwise XOR (Exclusive OR) được ký hiệu bằng ký tự `^` trong đa số các ngôn ngữ lập trình (Java, C++, Python, v.v.). Đây là một trong những phép toán thao tác ở mức bit (Bit Manipulation) quan trọng và mạnh mẽ nhất trong Khoa học Máy tính. Phép XOR chỉ trả về giá trị `1` (true) khi hai bit hạng tử khác biệt nhau, và trả về `0` (false) khi hai bit giống nhau. Sự đơn giản này dẫn đến vô số các tính chất toán học độc đáo, giúp chúng ta tối ưu hoá không gian và thời gian trong nhiều thuật toán, từ việc mã hoá dữ liệu, nén ảnh, đến việc giải các bài toán đếm hoặc tìm kiếm nhanh trên mảng.

## 2. Các khái niệm cơ bản (Basic Concepts)
- **Truth Table (Bảng chân trị):**
  - `0 ^ 0 = 0`
  - `0 ^ 1 = 1`
  - `1 ^ 0 = 1`
  - `1 ^ 1 = 0`
- **Bitwise Operation (Thao tác trên bit):** Phép XOR thực hiện so sánh song song trên từng cặp bit tương ứng của hai số nhị phân độc lập. Không có nhớ (carry) trong phép toán này.

## 3. Cấu trúc dữ liệu liên quan (Related Data Structures)
Mặc dù XOR là một phép toán, nó thường được ứng dụng để tối ưu hoá hoặc gắn liền với một số cấu trúc dữ liệu cụ thể:
- **Trie (Prefix Tree):** Dùng để giải các bài toán như "Maximum XOR of two numbers in an array".
- **Segment Tree / Fenwick Tree (Binary Indexed Tree):** Dùng để truy vấn XOR trên một đoạn của mảng (Prefix XOR Queries).
- **XOR Linked List:** Một biến thể của danh sách liên kết kép (Doubly Linked List) nhằm lưu trữ `next` và `prev` gộp vào một trường bộ nhớ duy nhất để tối ưu memory footprint.

## 4. Các thuật toán cơ bản (Basic Algorithms)
- **Prefix XOR:** Tính mảng cộng dồn nhưng bằng XOR thay vì dấu cộng (`+`). Cho phép trả lời nhanh truy vấn XOR của đoạn con liên tiếp `[L, R]` trong `O(1)`.
- **Rightmost Set Bit Truncation:** Sử dụng kết hợp XOR để tìm và tắt bit 1 ngoài cùng bên phải, thường dùng trong duyệt các tập con (subsets).
- **Adder Circuits:** Phép cộng hai số nhị phân luôn dùng một nửa XOR để ra Sum và một nửa AND để ra Carry. Thuật toán cộng không dùng dấu cộng (Bitwise Addition) bắt nguồn từ đây.

## 5. Tính chất và đặc điểm (Properties and Characteristics)
Những đẳng thức (Identities) này là "chìa khoá" để biến đổi công thức XOR:
1. **Bản sắc với số 0 (Identity Property):** `x ^ 0 = x`. (Một số XOR với 0 luôn bằng chính nó).
2. **Triệt tiêu lẫn nhau (Self-inverse / Nilpotent Property):** `x ^ x = 0`. (Bất kỳ số nào XOR với chính nó cũng bằng 0).
3. **Tính giao hoán (Commutative Law):** `A ^ B = B ^ A`.
4. **Tính kết hợp (Associative Law):** `A ^ (B ^ C) = (A ^ B) ^ C`.
5. **Đảo ngược (Inversion):** `x ^ 1s_complement(0) = ~x` hoặc `x ^ (đối với toàn bit 1) = NOT x`.
*Hệ quả:* XOR một loạt số thì thứ tự không quan trọng. Các phần tử giống nhau (có số lượng chẵn) sẽ tự triệt tiêu thành `0`, phần tử nào tồn tại lẻ lần sẽ giữ nguyên.

## 6. Các phép toán cơ bản (Basic Operations)
- **Toggle bit thứ `i`:** `x ^= (1 << i)`. Nếu bit `i` đang là 0 sẽ thành 1, đang 1 sẽ thành 0.
- **Hoán đổi hai số không dùng biến tạm (Swap without temp):**
  ```java
  a = a ^ b;
  b = a ^ b;
  a = a ^ b;
  ```
- **Xoá phần tử:** Nếu ta biết tổng XOR của tập con ban đầu, khi loại bỏ một phần tử `X`, ta chỉ cần tính `Total_XOR ^ X` để cập nhật tập con mới.

## 7. Các kỹ thuật tối ưu hóa (Optimization Techniques)
- **O(1) XOR Range 1 to N:** Để tìm `XOR(1, N)`, ta không cần vòng lặp O(N). Kết quả tuân theo quy luật chu kỳ 4 dựa trên `N % 4`:
  - Nếu `N % 4 == 0` -> XOR = `N`
  - Nếu `N % 4 == 1` -> XOR = `1`
  - Nếu `N % 4 == 2` -> XOR = `N + 1`
  - Nếu `N % 4 == 3` -> XOR = `0`
- **Masking / Bitmask:** Kết hợp XOR để đổi trạng thái của nhiều item trong kỹ thuật DP with Bitmask thay vì cộng trừ giá trị phức tạp.

## 8. Phân tích độ phức tạp (Complexity Analysis)
- **Thời gian (Time Complexity):** Phép toán XOR trên kiểu số nguyên (Integer 32/64 bits) thường mất đúng 1 clock cycle của CPU, tức là cực kỳ nhỏ, `O(1)` time.
- **Không gian (Space Complexity):** Cần duy trì các thanh ghi (registers), hoàn toàn mất `O(1)` memory. Khi dùng mảng Prefix XOR mất `O(N)` space.

## 9. Các lỗi thường gặp và cách tránh (Common Bugs and Pitfalls)
Dưới đây là 20 lỗi phổ biến khi làm việc với XOR:
1. Nhầm lẫn giữa toán tử Bitwise XOR (`^`) và phép luỹ thừa trong một số ngôn ngữ (như Python, C++ dùng `^` cho XOR).
2. Quên ngoặc khi kết hợp với so sánh: `a ^ b == 0` thường bị trình biên dịch hiểu thành `a ^ (b == 0)`.
3. Sử dụng XOR để swap nhưng gọi `swap(A[i], A[i])`, dẫn đến kết quả trả về bằng 0.
4. Bỏ qua Overflow khi ghép số tạo bitmask lớn (Integer max 32 bits thay vì Long 64 bits).
5. Áp dụng XOR cho mảng có số âm trong ngôn ngữ không kiểm soát chặt Two's complement gây lỗi logic bit dấu.
6. Hiểu sai `x ^ x = 0`: áp dụng khi tìm số xuất hiện 3 lần (Single Number II) nhưng không cẩn thận.
7. Gán nhầm `a = a ^ b` thay vì `a ^= b` dẫn đến biến đổi không đúng (ví dụ gán lầm sang `c`).
8. Cố dùng XOR đếm số lượng bit 1 thay vì dùng AND.
9. Quên tính giao hoán và kết hợp dẫn tới viết vòng lặp xử lý O(N^2) thay vì O(N).
10. Khởi tạo sai biến tích luỹ: `res = 1` thay vì `res = 0` (bản sắc của XOR là 0).
11. Single Number II: Áp dụng `res ^= A[i]` trực tiếp không ra kết quả vì 3 lần xuất hiện thì phép XOR dư lại số đó, nhưng hoà lẫn với số duy nhất.
12. Single Number III: Quên việc dùng mask `res & (-res)` để lấy rightmost set bit chia làm 2 mảng nhỏ.
13. Prefix XOR sai công thức: Viết nhầm `Prefix(R) ^ Prefix(L)` thay vì `Prefix(L-1)`.
14. Không xử lý mảng rỗng trong loop.
15. Quên rằng XOR thao tác bit song song, không có phép nhớ (carry) như phép cộng bình thường.
16. Nhầm bitwise XOR `^` với logical XOR (chỉ dùng cho boolean, một số ngôn ngữ không có kí hiệu chuẩn).
17. Dùng XOR trên Float hoặc Double gây Compiler error.
18. Không ép kiểu đúng khi làm việc byte/short, dẫn tới Sign Extension làm nhiễu bit cao.
19. Trích xuất bit quá đà bằng `1 << i` với `i > 31` trên số Integer 32 bit.
20. Dịch bit để XOR quên đóng ngoặc: `a ^ b << 1`.

## 10. Các trường hợp biên (Edge Cases)
Hãy xem xét 30 edge cases sau trong test case thực tế:
1. Mảng đầu vào trống.
2. Mảng có đúng 1 phần tử.
3. Tất cả phần tử đều là số 0.
4. Tất cả phần tử đều giống nhau (lượng chẵn -> 0, lẻ -> giá trị đó).
5. Truy vấn mảng Prefix XOR với `L = 0` (cần tránh `L-1` bị Index Out of Bounds).
6. Truy vấn Prefix XOR với `L = R`.
7. Các giá trị trong mảng là số âm (Negative Numbers).
8. Xen kẽ số âm và dương trong mảng.
9. Tràn số khi dịch bit kết hợp XOR.
10. `Integer.MAX_VALUE`.
11. `Integer.MIN_VALUE`.
12. XOR `0 ^ 0`.
13. Thao tác trích xuất `a ^ -a` (dễ nhầm với `a & -a`).
14. Kích thước mảng đạt ngưỡng lớn nhất có thể của ngôn ngữ.
15. Missing Number bài toán là `0`.
16. Missing Number bài toán là số lớn nhất `N`.
17. Hàm hoán vị Swap trên cùng một index `i`.
18. Yêu cầu tìm các số xuất hiện 1 lần nhưng có 3 số thoả mãn.
19. XOR các số từ 1 đến N nhưng N cực kỳ lớn (cần giải bằng O(1) time).
20. Khối dữ liệu liên tục theo chu kỳ chẵn lẻ.
21. Lấy rightmost set bit của `Integer.MIN_VALUE` (dễ tràn vì nó không có đối nguyên dương thật).
22. XOR của các bit toàn 1 (bitmask dạng `(1<<n)-1`).
23. Mask vượt ngưỡng kiểu nguyên int, quên `1L`.
24. Sử dụng byte stream qua XOR với các bộ mã hóa (UTF-8) không tương thích.
25. Mảng lặp số tự do không theo khuôn phép bài toán (thì XOR không giải quyết đơn giản được).
26. Hai số khác biệt nhau trong mảng là `0` và `Integer.MIN_VALUE`.
27. Đếm khoảng cách Hamming của số âm.
28. Cộng hai số dùng XOR vòng lặp vô hạn nếu tràn số / dấu.
29. Cập nhật xoá phần tử trong mảng tĩnh bằng XOR delta, thay vì dịch mảng.
30. Hash Collision quá cao nếu chỉ dùng XOR cho các bucket ngẫu nhiên (vì dễ ra 0).

## 11. Ứng dụng thực tế (Real-world Applications)
- **Mã hoá (Cryptography):** Phép XOR được dùng làm thao tác nòng cốt trong các hệ mật mã như One-Time Pad, AES, DES vì tính đảo ngược cực nhanh.
- **Raid 5 Disk Arrays:** XOR được sử dụng để tạo Parity check. Nếu một ổ đĩa hỏng, dữ liệu có thể khôi phục 100% bằng cách XOR các ổ còn lại với Parity block.
- **Checksums & CRC:** Kiểm tra tính toàn vẹn của gói tin qua mạng (Networking).
- **Computer Graphics:** Kỹ thuật XOR cursor (nhấp nháy con trỏ) vẽ hình ảnh mà không cần lưu trữ background, vẽ lần 1 để hiện, vẽ lần 2 để xoá về như cũ.

## 12. Các biến thể và mở rộng (Variations and Extensions)
- **Trie and XOR:** Sử dụng Trie để lưu trữ các dạng bit của mảng số nguyên. Điều này giúp dễ dàng di chuyển trên cây theo nguyên tắc "tham lam" (Greedy) chọn nhánh bit ngược để làm cực đại giá trị XOR.
- **Gaussian Elimination (Khử Gauss với XOR):** Trong đại số tuyến tính trên trường GF(2), phép cộng và trừ được thay bằng XOR. Dùng giải các bài toán về Subset XOR cực trị.

## 13. Các kỹ thuật gỡ lỗi (Debugging Techniques)
- **In dạng Bit:** Sử dụng `Integer.toBinaryString()` để trực quan hóa quá trình hai số được XOR.
- **Tách riêng bước tính toán:** Không lồng quá nhiều phép XOR và Shift vào một dòng (như `x ^= y ^= x ^= y`), nên tách thành từng biến trung gian để debug.
- **Kiểm tra ngoặc đơn:** Bất cứ khi nào kết hợp Bitwise với các phép toán Arithmetic hoặc Logical, luôn luôn dùng ngoặc bao.

## 14. So sánh với các phương pháp khác (Comparison with Other Methods)
| Tiêu chí | Sử dụng XOR | Sử dụng Hashing (HashMap) |
|---|---|---|
| Không gian bộ nhớ (Space) | O(1) | O(N) |
| Thời gian (Time) | O(N) xử lý trực tiếp | O(N) + overhead của Hash |
| Tính tổng quát | Hạn chế (chỉ dùng khi bài toán có tính triệt tiêu cặp) | Đa năng (có thể đếm bất kỳ tần suất nào) |
| Độ khó cài đặt | Dễ, mã nguồn rất ngắn gọn | Dài hơn, cần import thư viện |

## 15. Thực hành tốt nhất (Best Practices)
- Luôn cẩn thận với toán tử ưu tiên (Operator Precedence).
- Hãy nhớ công thức chu kỳ 4 khi tính XOR của các dãy liên tiếp.
- Chỉ nên dùng XOR trick để swap biến trong các câu hỏi phỏng vấn, không nên dùng trên code production vì không tiết kiệm thời gian hơn (do pipeline tối ưu hóa hiện đại) và giảm Readability.

## 16. Câu hỏi phỏng vấn thường gặp (Common Interview Questions)
20 câu hỏi phỏng vấn bạn cần nắm rõ:
1. Phép toán XOR hoạt động như thế nào ở mức bit? Tại sao `x^x=0` và `x^0=x`?
2. Hãy viết code hoán đổi hai biến số nguyên không sử dụng biến tạm thời, chứng minh tính đúng đắn.
3. Giải bài toán Single Number với O(1) bộ nhớ.
4. Có hai số xuất hiện một lần trong mảng, tất cả số khác xuất hiện hai lần. Tìm 2 số đó.
5. Missing Number: Làm sao để tìm số từ 0 đến N bị khuyết trong mảng bằng XOR?
6. Single Number II (xuất hiện 3 lần): XOR có giải được không? Phương pháp thay thế là gì?
7. Thực hiện phép tính tổng hai số mà không dùng toán tử `+`.
8. Ứng dụng tính giao hoán và kết hợp của XOR trong tối ưu thuật toán ra sao?
9. Hàm `f(N)` tính XOR của dãy `1..N` theo O(1) hoạt động như thế nào?
10. Cách giải mã một "XORed Array" nếu chỉ có mảng XOR và phần tử đầu tiên?
11. Tính số lượng lật bit (Min Flips) để `A OR B == C`. Tại sao XOR lại hữu ích ở đây?
12. Hamming Distance là gì? Làm sao tính bằng XOR cực nhanh?
13. Giải thích tại sao swap mảng bằng XOR `A[i]^=A[j]; A[j]^=A[i]; A[i]^=A[j]` sẽ biến số thành 0 nếu `i == j`?
14. Khi bài toán cần tìm hai số nguyên trong mảng có giá trị XOR lớn nhất, tại sao Trie là cấu trúc dữ liệu hoàn hảo?
15. Làm thế nào để tìm ra phần tử lặp số lần lẻ?
16. Tại sao XOR được dùng trong mật mã học và RAID 5?
17. Prefix XOR được định nghĩa và sử dụng như thế nào?
18. Có thể tạo Segment Tree với toán tử XOR không? (Có).
19. Phép tính `x & (-x)` liên quan đến XOR như thế nào khi phân chia 2 số độc lập?
20. Cách tìm một biến lạ trong số 4 biến giống nhau?

## 17. Nguồn tài liệu tham khảo (References)
- *Hacker's Delight* - Henry S. Warren Jr. (Cuốn sách kinh điển về tối ưu hóa phép toán nhị phân).
- *Introduction to Algorithms (CLRS)*.
- Hệ thống bài tập Leetcode phần "Bit Manipulation".

## 18. Các bài toán kinh điển (Classic Problems)
- **Single Number Series:** (I, II, III).
- **Missing Number.**
- **Maximum XOR of Two Numbers in an Array.**

## 19. Mối liên hệ với các chủ đề khác (Connections to Other Topics)
- **Toán rời rạc (Discrete Math):** XOR tương đương với phép cộng modulo 2 hoặc phép logic "Exclusive OR".
- **Dynamic Programming (Quy hoạch động):** Áp dụng chung với Bitmask.

## 20. Trực quan hóa và mô phỏng (Visualization and Simulation)
Mô phỏng phép tính Single Number với mảng `[4, 1, 2, 1, 2]`:
- Bắt đầu `res = 0`.
- XOR 4: `000 ^ 100 = 100` (4)
- XOR 1: `100 ^ 001 = 101` (5)
- XOR 2: `101 ^ 010 = 111` (7)
- XOR 1: `111 ^ 001 = 110` (6)
- XOR 2: `110 ^ 010 = 100` (4).
Kết quả trả về đúng số duy nhất là 4.

## 21. Lịch sử và sự phát triển (History and Evolution)
Phép toán XOR là một cấu phần vật lý cơ bản trong thiết kế các cổng logic điện tử (Logic Gates). Khái niệm này có nguồn gốc từ đại số Boolean (George Boole) thế kỷ 19.

## 22. Bài tập tự hành (Self-practice Exercises)
Hãy tự viết chương trình tìm `Prefix XOR` của một dãy số và truy vấn 10 đoạn ngẫu nhiên để kiểm tra sự am hiểu của bản thân. Tiếp theo, thử cài đặt phép toán `Max XOR Pair` sử dụng Trie.

## 23. Đánh giá và tổng kết (Summary and Evaluation)
Hiểu rõ tính chất XOR là bước đệm tuyệt vời để nâng cấp kỹ năng Problem Solving. Nó có vẻ phức tạp và bí ẩn đối với người mới bắt đầu, nhưng lại là công cụ sắc bén đối với một Lập trình viên Cạnh tranh (Competitive Programmer) hoặc một Kỹ sư Hệ thống.

## 24. Gợi ý tiếp theo (Next Steps)
- Ôn luyện bộ 30 bài tập về Bitwise XOR.
- Tìm hiểu về các toán tử Bitwise khác: AND, OR, Shift Left `<<`, Shift Right `>>`, Logical Shift Right `>>>`.
- Khám phá Bitmasking trong Quy hoạch động.
