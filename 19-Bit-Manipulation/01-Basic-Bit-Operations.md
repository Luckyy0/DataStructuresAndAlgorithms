# Basic Bit Operations (Các Thao Tác Bit Cơ Bản)

## 1. Tên chủ đề
Basic Bit Operations (Các thao tác bit cơ bản) trong Bit Manipulation.

## 2. Mục đích và tầm quan trọng
Bit Manipulation là kỹ thuật thao tác trực tiếp trên các bit của một số nguyên. Kỹ thuật này thường được sử dụng trong lập trình cấp thấp, đồ họa máy tính, mã hóa, mạng và tối ưu hóa hiệu suất vì các phép toán bit chạy rất nhanh trực tiếp trên CPU. Hiểu rõ các thao tác bit cơ bản là nền tảng để giải quyết nhiều bài toán phức tạp một cách tối ưu.

## 3. Các khái niệm cốt lõi
- **Bit**: Đơn vị thông tin nhỏ nhất trong máy tính, có giá trị 0 hoặc 1.
- **Toán hạng (Operands)**: Các số nguyên được xem như chuỗi các bit (thường là 32-bit hoặc 64-bit tùy hệ thống).
- **Phép toán Bitwise (Bitwise Operations)**: Các phép toán tác động lên từng bit của toán hạng.

## 4. Cách hoạt động
Máy tính biểu diễn các số nguyên dưới dạng nhị phân. Các toán tử bit thực hiện các phép logic trên từng cặp bit tương ứng của hai toán hạng (đối với phép toán hai ngôi) hoặc trên từng bit của một toán hạng (đối với phép toán một ngôi).

## 5. Đặc điểm và tính chất
- Nhanh: Các phép toán bit được CPU hỗ trợ ở mức phần cứng nên thực thi rất nhanh (thường chỉ mất 1 chu kỳ máy).
- Sử dụng bộ nhớ hiệu quả: Có thể sử dụng một số nguyên để lưu trữ nhiều cờ boolean (bitmasking).
- Gọn gàng: Giải quyết các bài toán nhất định bằng ít dòng code hơn so với các phương pháp thông thường.

## 6. Ưu điểm và nhược điểm
**Ưu điểm:**
- Tốc độ thực thi cực kỳ nhanh.
- Giảm thiểu dung lượng bộ nhớ (sử dụng bit thay vì boolean).
- Rất hiệu quả trong một số thuật toán và cấu trúc dữ liệu cụ thể (như Fenwick Tree, Trie, giải quyết bài toán Subset).

**Nhược điểm:**
- Khó đọc, khó hiểu (đặc biệt đối với người mới bắt đầu).
- Dễ sinh lỗi logic khó debug (ví dụ: nhầm lẫn độ ưu tiên toán tử).
- Phụ thuộc vào ngôn ngữ và kiến trúc hệ thống (số âm, kích thước số nguyên).

## 7. Các toán tử Bitwise (Classifications)
- **AND (`&`)**: Trả về 1 nếu cả 2 bit đều là 1.
- **OR (`|`)**: Trả về 1 nếu có ít nhất 1 bit là 1.
- **XOR (`^`)**: Trả về 1 nếu 2 bit khác nhau.
- **NOT (`~`)**: Đảo ngược bit (1 thành 0, 0 thành 1).
- **Left Shift (`<<`)**: Dịch các bit sang trái.
- **Right Shift (`>>`)**: Dịch các bit sang phải (bảo toàn dấu - Arithmetic Shift hoặc không bảo toàn dấu - Logical Shift `>>>`).

## 8. Các thao tác cơ bản (Basic Operations)
- **AND, OR, XOR, NOT, Left Shift, Right Shift**
- **Checking parity (odd/even)**: Kiểm tra chẵn/lẻ bằng `x & 1`. Nếu bằng 1 là số lẻ, bằng 0 là số chẵn.
- **Check the i-th bit**: Lấy trạng thái bit thứ i bằng `(x & (1 << i)) != 0`.
- **Set the i-th bit**: Bật bit thứ i lên 1 bằng `x | (1 << i)`.
- **Clear the i-th bit**: Tắt bit thứ i về 0 bằng `x & ~(1 << i)`.
- **Toggle the i-th bit**: Đảo ngược bit thứ i bằng `x ^ (1 << i)`.

## 9. Các thuật toán liên quan
- Kỹ thuật Bitmasking.
- Duyệt qua tất cả các tập con (Subsets).
- Brian Kernighan's Algorithm (Đếm số bit 1).
- Các thuật toán trong Đồ họa máy tính, Mật mã học.

## 10. Độ phức tạp thời gian và không gian
- **Thời gian**: O(1) cho các thao tác bit đơn lẻ. O(L) với L là số bit (thường là 32 hoặc 64) cho các thao tác trên toàn bộ bit (ví dụ: đếm số lượng bit 1).
- **Không gian**: O(1).

## 11. Cách triển khai (Implementation)
```java
public class BitOperations {
    public static boolean isOdd(int n) {
        return (n & 1) == 1;
    }

    public static boolean checkBit(int n, int i) {
        return (n & (1 << i)) != 0;
    }

    public static int setBit(int n, int i) {
        return n | (1 << i);
    }

    public static int clearBit(int n, int i) {
        return n & ~(1 << i);
    }

    public static int toggleBit(int n, int i) {
        return n ^ (1 << i);
    }
}
```

## 12. Ứng dụng thực tế
- Quyền truy cập (Permissions/Flags) trong hệ điều hành hoặc hệ thống file.
- Nén dữ liệu (Data compression).
- Các thuật toán băm (Hashing) và mã hóa (Cryptography).
- IP Routing và thao tác trên địa chỉ mạng.

## 13. Các mẫu câu hỏi thường gặp
- Tìm phần tử duy nhất xuất hiện một lần trong mảng (sử dụng XOR).
- Đếm số lượng bit 1 trong biểu diễn nhị phân (Hamming Weight).
- Kiểm tra một số có phải là lũy thừa của 2.
- Tạo tập con (Subset generation).

## 14. Cấu trúc dữ liệu hoặc thuật toán thay thế
Thay vì dùng bitmasking, có thể sử dụng mảng boolean `boolean[]` hoặc `BitSet` (trong Java). Tuy nhiên sẽ tiêu tốn nhiều bộ nhớ hơn và không tận dụng được tốc độ xử lý của CPU như phép toán số nguyên trực tiếp.

## 15. 20 Lỗi thường gặp (Common Bugs)
1. Nhầm lẫn giữa bitwise AND (`&`) và logical AND (`&&`).
2. Nhầm lẫn giữa bitwise OR (`|`) và logical OR (`||`).
3. Thiếu dấu ngoặc đơn: Các toán tử bit có độ ưu tiên thấp hơn các toán tử so sánh (ví dụ: `a & b == 0` được hiểu là `a & (b == 0)`).
4. Shift tràn bit: Dịch bit một số nguyên 32-bit vượt quá 31 vị trí. Hành vi phụ thuộc ngôn ngữ.
5. Quên không bảo toàn dấu khi Right Shift: Sử dụng `>>` thay vì `>>>` (trong Java) đối với số không dấu.
6. Tính nhầm vị trí i (tính từ 1 thay vì tính từ 0).
7. Sử dụng kiểu `int` (32 bit) cho bitmask cần nhiều hơn 32 giá trị (lẽ ra phải dùng `long`).
8. Dùng `1 << i` khi i >= 32 mà không ép kiểu `1L << i`.
9. Thao tác trên số âm và không hiểu rõ biểu diễn Two's Complement.
10. `a ^ a = 0` nhưng lại nhầm thành `a ^ a = a`.
11. `a ^ 0 = a` nhưng lại nhầm thành `a ^ 0 = 0`.
12. Đảo ngược bit của số không mong muốn bằng toán tử NOT (`~`) trên biến kiểu nhỏ (như byte, short) bị thăng hạng thành `int` trong Java.
13. Kiểm tra chẵn lẻ bằng `% 2` đối với số âm có thể trả về -1 trong một số ngôn ngữ, thay vì dùng `& 1`.
14. Nhầm lẫn khi trích xuất bit bằng cách `(n >> i) == 1` thay vì `(n >> i) & 1 == 1`.
15. Vô tình thay đổi giá trị biến: Thay vì `n = n | (1<<i)`, lại viết `n | (1<<i)` nhưng không gán lại.
16. Tràn số khi nhân với lũy thừa 2 bằng phép Shift Left mà không kiểm tra giới hạn.
17. Clear n bit cuối bằng cách dùng `~0 << n` thay vì cẩn thận với biểu diễn nhị phân.
18. Trích xuất n bit thấp nhất sai công thức: `n & ((1 << k) - 1)`.
19. Không xét số 0 ở một số bài toán như tìm số bit 1.
20. Trả về true/false dựa trên giá trị của bitmask thay vì so sánh trực tiếp với 0. Ví dụ: `return n & mask` (lỗi biên dịch trong Java).

## 16. 30 Trường hợp kiểm thử biên (Edge Cases)
1. Số 0 (`0x00000000`).
2. Số 1 (`0x00000001`).
3. Số âm lớn nhất (`Integer.MIN_VALUE`, `0x80000000`).
4. Số dương lớn nhất (`Integer.MAX_VALUE`, `0x7FFFFFFF`).
5. Số -1 (`0xFFFFFFFF`).
6. Dịch bit sang trái 0 vị trí.
7. Dịch bit sang phải 0 vị trí.
8. Set bit ở vị trí 0.
9. Set bit ở vị trí 31.
10. Clear bit ở vị trí 31 của số dương và âm.
11. Số có tất cả các bit là 1.
12. Số có bit 1 và 0 xen kẽ (`0x55555555`).
13. Số có bit 0 và 1 xen kẽ (`0xAAAAAAAA`).
14. Dịch trái với giá trị âm (Shift negative amount - thường lỗi hoặc UB).
15. Lũy thừa của 2 nhỏ nhất (`2^0 = 1`).
16. Lũy thừa của 2 lớn nhất có thể (`2^30`).
17. Dịch một số nhỏ (ví dụ 1) quá số bit của kiểu dữ liệu.
18. Xóa toàn bộ số bit của một số.
19. Toán tử XOR hai số bằng nhau.
20. Toán tử XOR số dương và số âm.
21. Thao tác trên các số lớn hơn kiểu Integer (cần đổi sang Long).
22. Các số có rất ít bit 1 (ví dụ chỉ có bit cao nhất và bit thấp nhất).
23. Sử dụng Logical Shift Right (`>>>`) trên số nguyên âm.
24. Sử dụng Arithmetic Shift Right (`>>`) trên số nguyên âm.
25. Toggle bit thứ 31 để đổi dấu (không hoàn toàn đổi dấu vì 2's complement).
26. Set bit trên kiểu giá trị byte, short sau đó ép kiểu.
27. Đếm số bit 1 đối với `Integer.MIN_VALUE` (chỉ có 1 bit 1).
28. Đếm số bit 1 đối với -1 (có 32 bit 1).
29. Thao tác bit liên hoàn có thể gây tràn số không mong muốn.
30. Clear bit thứ i khi bit đó đã là 0.

## 17. 20 Câu hỏi phỏng vấn
1. Làm thế nào để kiểm tra một số có phải là lũy thừa của 2 hay không?
2. Viết hàm đếm số bit 1 của một số nguyên.
3. Tìm phần tử duy nhất xuất hiện 1 lần trong mảng, các phần tử khác xuất hiện 2 lần.
4. Tìm hai phần tử xuất hiện 1 lần trong mảng, các phần tử khác xuất hiện 2 lần.
5. Đảo ngược các bit của một số nguyên 32 bit.
6. Không dùng biến phụ, hãy hoán đổi giá trị của 2 biến.
7. Xóa bit 1 ở vị trí thấp nhất (least significant set bit) của một số.
8. Lấy bit 1 ở vị trí thấp nhất của một số.
9. Kiểm tra xem có 2 bit 1 nào nằm kề nhau không.
10. Cộng hai số nguyên mà không dùng toán tử `+` hoặc `-`.
11. Tạo một số chỉ có các bit 1 từ vị trí i đến j.
12. Tìm phần tử xuất hiện 1 lần, các phần tử khác xuất hiện 3 lần.
13. Làm sao để nhân một số với 7 bằng toán tử bit?
14. Chia một số cho 2 bằng thao tác bit.
15. Tính XOR từ 1 đến N trong O(1).
16. Tìm số lượng bit cần thiết để chuyển từ số A sang số B.
17. Chuyển đổi ký tự viết thường sang viết hoa bằng Bitwise.
18. Thiết lập k bit đầu tiên từ phải sang trái.
19. Giải thích sự khác biệt giữa `>>` và `>>>` trong Java.
20. Trích xuất n bit thấp nhất của số x.

## 18. Tài liệu tham khảo
- Bit Twiddling Hacks by Sean Eron Anderson.
- "Hacker's Delight" by Henry S. Warren Jr.

## 19. Mẹo và thủ thuật
- `x & (x - 1)` xóa bit 1 ở vị trí phải nhất.
- `x & -x` lấy bit 1 ở vị trí phải nhất (các bit khác trở thành 0).
- `x ^ x = 0`.
- Luôn sử dụng ngoặc đơn khi kết hợp thao tác bit và các toán tử so sánh do độ ưu tiên toán tử: `if ((x & 1) == 0)` thay vì `if (x & 1 == 0)`.
- Chuyển HOA thành thường: `ch | ' '`.
- Chuyển thường thành HOA: `ch & '_'`.

## 20. Mối liên hệ với các chủ đề khác
- Toán học (Combinatorics, Đếm).
- Đồ thị (Bitmask DP cho bài toán người chào hàng - TSP).
- Mảng và Chuỗi (Dùng Bitmask để lưu trữ các ký tự đã dùng).

## 21. Bài tập thực hành
Thực hành các thao tác cơ bản:
- Implement lại các phép thử parity, check, set, clear, toggle.
- Cố gắng áp dụng `x & (x - 1)` để tối ưu các vòng lặp đếm.

## 22. Câu hỏi trắc nghiệm
Q: Đoạn mã `x & (x - 1) == 0` dùng để kiểm tra điều gì?
A. Kiểm tra xem x có phải là số nguyên tố không.
B. Kiểm tra xem x có phải là số lẻ không.
C. Kiểm tra xem x có phải là lũy thừa của 2 không (hoặc x = 0).
D. Kiểm tra xem x có chia hết cho 2 không.
(Đáp án đúng là C)

## 23. Tổng kết
Các thao tác bit là công cụ vô cùng lợi hại giúp tối ưu thời gian, không gian. Việc nắm vững cách thao tác trên từng bit, cùng các đặc trưng của AND, OR, XOR sẽ giải quyết nhanh chóng các bài toán phức tạp liên quan đến tính chẵn lẻ, hoán vị, tập hợp, và các biến cờ (flags).

## 24. Đánh giá mức độ hiểu bài
- [ ] Tôi biết ý nghĩa của `&`, `|`, `^`, `~`, `<<`, `>>`, `>>>`.
- [ ] Tôi có thể thiết lập, xóa, đảo ngược, hoặc lấy giá trị của một bit bất kỳ.
- [ ] Tôi hiểu vì sao cần phải dùng dấu ngoặc cho thao tác bit.
- [ ] Tôi có thể giải thích được công dụng của `n & (n - 1)`.
