# Chuyên đề 5: Math và Bit Manipulation (Toán học và Thao tác Bit)

## 1. Giới thiệu (Introduction)
Sự kết hợp giữa **Math (Toán học)** và **Bit Manipulation (Thao tác bit)** là một trong những chủ đề khó nhưng cực kỳ quan trọng trong khoa học máy tính. Nó không chỉ giúp tối ưu hóa thuật toán về mặt thời gian (nhờ các phép toán bit chạy trực tiếp trên ALU của CPU) mà còn giúp giải quyết các bài toán số học phức tạp mà không cần đến các toán tử thông thường.

## 2. Các khái niệm cơ bản (Basic Concepts)
Các phép toán cơ bản bao gồm `AND` (`&`), `OR` (`|`), `XOR` (`^`), `NOT` (`~`), `Shift Left` (`<<`), và `Shift Right` (`>>`, `>>>`).
Trong toán học nhị phân, `XOR` hoạt động như phép cộng không nhớ (addition without carry), và `AND` đi kèm với dịch trái (`<< 1`) đóng vai trò lưu trữ bit nhớ (carry).

## 3. Phép cộng không dùng toán tử `+` (Addition without `+`)
Để cộng hai số `a` và `b` mà không dùng `+`:
- Cấp 1: Phép cộng không có nhớ (carry) chính là `a ^ b`.
- Cấp 2: Các bit nhớ được tính bằng `(a & b) << 1`.
- Lặp lại quá trình này cho đến khi bit nhớ (carry) bằng `0`.

## 4. Phép trừ không dùng toán tử `-` (Subtraction without `-`)
Tương tự phép cộng, phép trừ `a - b` thực chất là `a + (-b)`.
- Trong hệ đếm bù 2 (two's complement), `-b = ~b + 1`.
- Nếu tính trực tiếp bằng bit: `a - b`. Phép trừ không có mượn (borrow) là `a ^ b`.
- Bit mượn được tính bằng `(~a & b) << 1`. Lặp lại cho đến khi mượn bằng `0`.

## 5. Phép nhân dùng dịch bit (Multiplication using Shifts)
Để nhân hai số `a` và `b`:
- Có thể sử dụng thuật toán nhân kiểu Nga (Russian Peasant Multiplication).
- Nếu `b` lẻ, cộng thêm `a` vào kết quả. Sau đó `a` dịch trái 1 bit (`a << 1`), `b` dịch phải 1 bit (`b >> 1`). Lặp đến khi `b = 0`.

## 6. Phép chia dùng dịch bit (Division using Shifts)
Để chia `dividend` cho `divisor`:
- Dịch trái `divisor` cho đến khi nó lớn nhất nhưng vẫn nhỏ hơn `dividend`.
- Trừ `dividend` cho `divisor` đã dịch bit, và cộng thêm `1 << số_lần_dịch` vào thương. Lặp lại quá trình này.

## 7. Mã Gray (Gray Code Sequence Generation)
**Gray Code** là chuỗi số nhị phân trong đó hai giá trị liên tiếp chỉ khác nhau đúng 1 bit.
- Công thức chuyển số thập phân `n` sang Gray code: `G(n) = n ^ (n >> 1)`.
- Ứng dụng rộng rãi trong sửa lỗi, giải thuật quay lui và phần cứng.

## 8. Cấu trúc dữ liệu và Bit Manipulation
Sử dụng các bit nguyên như một tập hợp (Bitset/Bitmask). Thay vì mảng boolean, ta dùng một số nguyên 32 hoặc 64 bit để tối ưu không gian O(1).

## 9. Tính chẵn lẻ (Parity Mathematics)
**Parity** đếm số bit `1` trong một số là chẵn hay lẻ. Có thể tính nhanh Parity của số nguyên bằng các phép `XOR` dịch phải và `n & (n - 1)`.

## 10. Phép toán mô phỏng cổng logic (Simulating Logic Gates)
- Half Adder: Dùng `XOR` và `AND`.
- Full Adder: Mở rộng với 3 input.
- Mạch nhân và chia logic.

## 11. Ứng dụng thực tế (Real-world Applications)
- **Cryptography** (Mật mã học): Các thuật toán như AES, DES phụ thuộc mạnh vào dịch bit và XOR.
- **Data Compression** (Nén dữ liệu): Thuật toán Huffman, LZW.
- **Networking**: Tính toán subnet mask, checksum.

## 12. Thuật toán Lũy thừa nhị phân (Binary Exponentiation)
Tính `a^b` trong thời gian O(log b). Dựa vào bit của `b`:
- `b & 1 == 1`: `res = res * a`.
- Luôn cập nhật `a = a * a`, `b >>= 1`.

## 13. Thuật toán Binary GCD
Tính ước chung lớn nhất (UCLN) tối ưu cho phần cứng bằng cách dùng shift và trừ thay vì phép chia modulo (`%`), với độ phức tạp O(log(min(a, b))).

## 14. So sánh Bitwise vs Arithmetic Operations (Comparison)
- Tốc độ: Phép toán bit thường được tối ưu trực tiếp bằng mã máy (machine instructions), trong khi `*`, `/`, `%` cần nhiều chu kỳ CPU hơn.
- Tràn số (Overflow): Tính toán bit an toàn hơn nếu biết kiểm soát, nhưng nguy hiểm trong trường hợp dùng sai kiểu dữ liệu (signed/unsigned).

## 15. Tối ưu hóa bộ nhớ và tốc độ (Optimization)
- Dùng `a & (a - 1)` thay cho modulo để kiểm tra lũy thừa 2.
- Dùng `a & ((1 << k) - 1)` thay cho `a % (2^k)`.

## 16. Phân tích độ phức tạp (Complexity Analysis)
- **Time Complexity:** Hầu hết các phép biến đổi đơn giản chạy trong O(1). Các thuật toán nhân/chia chạy trong O(log n) (với n là giá trị) hay O(32)/O(64).
- **Space Complexity:** O(1) do chỉ thao tác với số nguyên biến đơn.

## 17. 20 Câu hỏi phỏng vấn (20 Interview Questions)
1. Cài đặt phép tính tổng 2 số mà không dùng toán tử.
2. Cài đặt phép tính trừ 2 số mà không dùng toán tử.
3. Làm sao để chia hai số nguyên mà không dùng `*`, `/`, `%`?
4. Đảo ngược các bit của một số nguyên 32-bit.
5. Sinh dãy mã Gray cho số bit n.
6. Tính luỹ thừa số nguyên nhanh (Binary Exponentiation).
7. Kiểm tra một số có phải là bội của 3 không qua bit?
8. Cài đặt Binary GCD.
9. Đếm số set-bits (Hamming Weight) tối ưu nhất.
10. Tìm số xuất hiện 1 lần trong mảng, các số khác xuất hiện 3 lần (dùng toán học modulo 3 trên bit).
11. Tính căn bậc hai nguyên bằng thao tác bit.
12. Đảo ngược vị trí hai số không dùng biến tạm.
13. Tính giá trị tuyệt đối không dùng rẽ nhánh (`abs(x) = (x ^ (x >> 31)) - (x >> 31)`).
14. Xác định bit thứ k của kết quả tích hai số mà không tính cả tích.
15. Tính `n!` chia hết cho lũy thừa cao nhất của 2.
16. Tìm số nhỏ nhất lớn hơn N có cùng số lượng bit `1`.
17. Phép chiếu (masking) trên mảng hai chiều.
18. Biến đổi mảng bit bằng toán tử XOR đồng loạt.
19. Tính tổng các khoảng cách Hamming giữa tất cả các cặp số.
20. Giải bài toán Nim Game sử dụng tổng XOR (Nim-Sum).

## 18. 20 Lỗi thường gặp (20 Common Bugs)
1. **Integer Overflow:** Không xử lý tràn số khi dịch trái (`<< 31`).
2. **Signed Shift vs Unsigned Shift:** Dùng nhầm `>>` (giữ dấu) và `>>>` (không giữ dấu) trong Java.
3. **Mất bit tràn:** Lưu carry vào int khi tính quá 32-bit (sẽ bị âm và sai chu kỳ).
4. **Modulo số âm:** Tính nhầm parity nếu số âm không mask `& 1`.
5. **Độ ưu tiên toán tử:** Viết `a & b == 0` thay vì `(a & b) == 0`.
6. **Lỗi Edge Case của Integer.MIN_VALUE:** `Math.abs(Integer.MIN_VALUE)` trả về số âm, dịch phải sẽ sai sót.
7. **Chia cho 0:** Thuật toán chia quên kiểm tra `divisor == 0`.
8. **Vòng lặp vô hạn:** Khi carry không bao giờ biến mất do tính bù 2 sai.
9. **Nhầm lẫn XOR và OR:** Khi cần tính tổng hợp, sử dụng XOR thay vì OR làm sai mask.
10. **Lỗi số thập phân:** Phép bit không áp dụng trực tiếp lên `float`/`double`.
11. **Type Casting ngầm:** Chuyển từ `byte` sang `int` bị chèn số 1 ở các bit cao (sign extension).
12. **Tính toán bit trên char:** Quên rằng `char` trong Java là unsigned 16-bit.
13. **Shift lớn hơn 32:** Trong Java, dịch bit bị tự động chia lấy dư (`n << 35` tương đương `n << 3`).
14. **Quên cập nhật kết quả trong vòng lặp Bitmask:** `res |= (1 << i)` nhưng quên gán.
15. **Dịch trái mảng quá chiều dài:** Tạo bitmask cho tập hợp lớn hơn 32 phần tử bằng số nguyên thường thay vì `long`.
16. **Sai lầm về XOR mảng tự thân:** `a ^= a` làm biến mất giá trị thành `0`.
17. **Cộng các biến boolean:** Không thể cộng boolean, phải ép kiểu nguyên trước khi XOR/AND.
18. **Không check null:** Mảng rỗng bị đẩy vào thuật toán xử lý hàng loạt XOR.
19. **Lỗi endianness:** Mặc định thuật toán bit luôn hoạt động giống nhau, nhưng khi serialization có thể phụ thuộc kiến trúc.
20. **Nhầm vị trí MSB/LSB:** Tính nhầm vị trí bit thứ 0 và thứ 31.

## 19. 30 Trường hợp biên (30 Edge Cases)
1. `0 + 0`
2. `0 - 0`
3. Tính toán với `Integer.MAX_VALUE`
4. Tính toán với `Integer.MIN_VALUE`
5. `Integer.MAX_VALUE + 1` (gây tràn số)
6. `Integer.MIN_VALUE - 1`
7. Phép trừ mà số bị trừ nhỏ hơn (tạo bù 2 âm).
8. Cả hai toán hạng đều âm.
9. Một toán hạng là `0`.
10. `a << 32` (Trong Java, nó bằng `a << 0` = `a`).
11. `a >> 32` và `a >>> 32`.
12. Chia cho `1`.
13. Chia cho `-1` (đặc biệt `Integer.MIN_VALUE / -1` gây tràn số).
14. Chia cho chính nó `a / a`.
15. Số bằng chính lũy thừa của 2 (Power of 2).
16. Số bằng lũy thừa của 2 trừ đi 1 (VD: 15, 31).
17. Số chỉ toàn bit 0.
18. Số chỉ toàn bit 1 (`-1`).
19. Mẫu số Gray Code với `n = 0` (chỉ có `[0]`).
20. Mã Gray Code với `n = 16` (sinh mảng lớn gây Memory Limit Exceeded).
21. Thuật toán sinh mảng nhưng mảng có kích thước 1.
22. Nhập vào bitmask bị vượt quá giới hạn long (`> 64 bits`).
23. Tính toán parity với `0`.
24. Bitmask XOR với tất cả phần tử trong mảng đều bằng nhau.
25. Thuật toán GCD(a, b) với `a = 0` hoặc `b = 0`.
26. Luỹ thừa nhị phân: Cở số `0`, số mũ `0`.
27. Luỹ thừa số mũ âm (Negative Exponent).
28. Tính tổng hoặc phép bit có dính số `NaN` (nếu convert từ Float).
29. Cập nhật mảng trên chỉ số bit lớn hơn mảng cung cấp.
30. Tìm số xuất hiện đơn lẻ trong dãy chỉ có 1 phần tử.

## 20. Debugging (Gỡ lỗi)
Khi gỡ lỗi các thuật toán Math/Bitwise:
- Sử dụng hàm in số nhị phân: `Integer.toBinaryString(n)`. Để dễ quan sát, định dạng thêm số 0 ở đầu (padding zeros).
- Theo dõi `carry` hoặc `borrow` qua từng vòng lặp. Nếu vòng lặp không dừng, hãy kiểm tra mask có đúng chiều độ dài bit hay không.

## 21. Thư viện hỗ trợ (Supported Libraries)
- `Integer.bitCount()`
- `Integer.highestOneBit()` / `Integer.lowestOneBit()`
- `Integer.numberOfLeadingZeros()`
- Lớp `java.util.BitSet` nếu cần xử lý lượng bit lớn hơn 64.
- Lớp `BigInteger` của Java cung cấp `BigInteger.testBit()`, `BigInteger.setBit()` cho số cực lớn.

## 22. Mẹo vặt (Tips and Tricks)
- Trích xuất bit thấp nhất: `n & -n`.
- Xóa bit thấp nhất bằng 1: `n & (n - 1)`.
- Chuyển thành số dư của lũy thừa 2: `x & (2^k - 1)`.

## 23. Test cases
Tạo hàm Test Framework chuẩn:
```java
assert(add(5, 7) == 12);
assert(add(-5, -7) == -12);
assert(add(Integer.MAX_VALUE, 0) == Integer.MAX_VALUE);
assert(divide(10, 3) == 3);
assert(divide(-2147483648, -1) == 2147483647);
```

## 24. Tổng kết (Conclusion)
Math và Bit Manipulation cung cấp các công cụ sắc bén nhất để tối ưu hóa tận gốc của tính toán. Tuy mã nguồn sử dụng phép tính bit khó đọc, độ hiệu quả của nó là không thể thay thế trong hệ thống thời gian thực (real-time systems) hay xử lý dữ liệu luồng (stream data).
