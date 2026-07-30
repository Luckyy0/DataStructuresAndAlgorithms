# 04 - Advanced Bit Tricks

## 1. Giới thiệu (Introduction)
Các kỹ thuật **Advanced Bit Tricks** là nền tảng để giải quyết nhiều bài toán phức tạp một cách tối ưu nhất. Khác với các thao tác bit cơ bản, các kỹ thuật nâng cao thường kết hợp toán học (ví dụ: số học bù 2) hoặc kết hợp với các cấu trúc dữ liệu khác như **Trie** để giải quyết bài toán với thời gian cực nhanh (thường là $O(1)$ hoặc $O(32)$).

## 2. Khái niệm cơ bản (Basic Concepts)
- **Brian Kernighan's Algorithm (`n & (n-1)`)**: Xóa bit 1 thấp nhất (rightmost set bit). Kỹ thuật này thường được dùng để đếm số lượng bit 1 của một số, vì thời gian chạy tỉ lệ thuận với số lượng bit 1.
- **Isolate Lowest Set Bit (`n & (-n)`)**: Trích xuất bit 1 thấp nhất của số $n$. Trả về một giá trị chỉ có đúng một bit 1 ở vị trí tương ứng. Dùng phổ biến trong cấu trúc **Fenwick Tree (Binary Indexed Tree)**.
- **Tries + Bit Manipulation**: Lưu trữ biểu diễn nhị phân của số vào một cây Trie để truy vấn hiệu quả các phép tính liên quan đến XOR, giúp giải quyết bài toán tìm Max XOR.

## 3. Cấu trúc dữ liệu (Data Structures)
- **Bitwise Trie**: Mỗi node có tối đa 2 con (tương ứng với bit 0 và bit 1). Thường dùng mảng hoặc đối tượng để lưu trữ các con đường nhị phân. Độ sâu của Trie thường là 32 (cho số nguyên 32-bit).

## 4. Phân tích độ phức tạp (Complexity Analysis)
- **Thời gian (Time Complexity)**:
  - Khử bit 1 thấp nhất / trích xuất bit: $O(1)$.
  - Truy vấn / Chèn vào Bitwise Trie: $O(K)$, trong đó $K$ là số lượng bit (thường $K=32$ cho Java int). Điều này có thể coi là $O(1)$.
- **Không gian (Space Complexity)**:
  - Phép toán bit thuần: $O(1)$.
  - Bitwise Trie: $O(N \times K)$, với $N$ là số phần tử và $K$ là số bit.

## 5. Ứng dụng thực tế (Real-world Applications)
- **Hệ thống mạng**: Tính toán Subnet mask, routing.
- **Xử lý tín hiệu**: Phân tích bit chẵn lẻ (parity check), checksum, sửa lỗi (Error Correction Codes).
- **Mật mã học (Cryptography)**: Các phép XOR được dùng rộng rãi trong mã hóa.
- **Đồ họa máy tính**: Tính toán màu sắc bằng phép dịch và che mask bit (Bitmasking).

## 6. Các thuật toán cơ bản (Basic Algorithms)
- **Kiểm tra lũy thừa của 2**: Một số dương là lũy thừa của 2 nếu và chỉ nếu `(n & (n - 1)) == 0`.
- **Đếm số lượng bit 1 (Hamming Weight)**:
  ```java
  int count = 0;
  while (n > 0) {
      n &= (n - 1);
      count++;
  }
  ```

## 7. Các thuật toán nâng cao (Advanced Algorithms)
- **Tìm cặp có XOR lớn nhất**: Dùng Bitwise Trie. Chèn toàn bộ các số vào Trie. Với mỗi số, đi theo các nhánh nhị phân ngược lại bit hiện tại của nó (nếu nhánh đó tồn tại) để tối đa hóa XOR.
- **Xóa tất cả các bit trừ bit 1 thấp nhất**: Sử dụng `n & (-n)`.
- **Đảo ngược các bit**: Dùng Bit manipulation chia để trị (phương pháp mask).
- **Gopher's Hack / Gosper's Hack**: Sinh tất cả các tập hợp có cùng số lượng bit 1.

## 8. Mẫu code cơ bản (Basic Code Templates)
```java
// Trie Node for Max XOR
class TrieNode {
    TrieNode[] children = new TrieNode[2];
}

// Insert into Bitwise Trie
public void insert(TrieNode root, int num) {
    TrieNode node = root;
    for (int i = 31; i >= 0; i--) {
        int bit = (num >> i) & 1;
        if (node.children[bit] == null) {
            node.children[bit] = new TrieNode();
        }
        node = node.children[bit];
    }
}
```

## 9. Các mẫu thiết kế (Design Patterns)
- **Flyweight Pattern**: Tái sử dụng các Node trong Trie để tiết kiệm bộ nhớ khi có nhiều bit trùng lặp ở tiền tố.

## 10. Kỹ thuật tối ưu hóa (Optimization Techniques)
- Tránh khởi tạo quá nhiều mảng con. Sử dụng mảng tuyến tính hoặc ma trận 2D tĩnh `trie[MAX_NODES][2]` thay vì tạo đối tượng `TrieNode` bằng `new` trong các cuộc thi lập trình (Competitive Programming) để tối ưu allocation overhead.
- Hạn chế phép nhân/chia, luôn ưu tiên phép dịch bit `<<` và `>>` khi làm việc với lũy thừa của 2.

## 11. Lỗi thường gặp (20 Common Bugs)
1. Dùng `n & (n-1)` để kiểm tra lũy thừa của 2 mà quên kiểm tra `n > 0`.
2. Tràn số integer (Integer Overflow) khi dùng `1 << i` với `i >= 31`. Phải dùng `1L << i`.
3. Phân biệt sai giữa phép dịch phải có dấu `>>` và không dấu `>>>` trong Java.
4. Bỏ qua độ ưu tiên toán tử: viết `if (n & 1 == 0)` thay vì `if ((n & 1) == 0)`.
5. Đặt kích thước Trie mảng tĩnh quá nhỏ dẫn đến `IndexOutOfBounds`.
6. Lấy bit sai: `(n >> i)` thay vì `(n >> i) & 1`.
7. Dùng phép cộng thay vì phép `OR` khi hai bit đang xử lý có thể chồng lên nhau.
8. Quên trừ đi phần tử đã dùng khi thực hiện thao tác xóa bit.
9. Xử lý thiếu trường hợp số âm cho các phép tính số học bù 2.
10. Bitmask khởi tạo là 0 thay vì `0xFFFFFFFF` khi cần khởi tạo mask bật toàn bộ bit.
11. Dịch `>> 32` đối với số nguyên 32-bit (trong Java nó sẽ dịch `0` bit).
12. Vòng lặp vô hạn khi dịch bit số âm bằng `>>` thay vì `>>>`.
13. Xử lý sai vị trí node trong Bitwise Trie khi quên đi lùi dần từ `31` về `0`.
14. Áp dụng `n & (-n)` trên `n = 0` mong đợi kết quả là một vị trí hợp lệ.
15. Không tính tới độ dài số bit khi padding số nguyên.
16. Nhầm `XOR` (`^`) với toán tử lũy thừa.
17. Dùng `&` nhầm thành `&&` (Logic AND).
18. Không xóa sạch Trie khi xử lý nhiều test cases liên tiếp.
19. Gán sai giá trị cho nút lá trong Bitwise Trie.
20. Trả về sai Max XOR khi cây Trie rỗng.

## 12. Trường hợp biên (30 Edge Cases)
1. `n = 0`.
2. `n = 1`.
3. `n = -1`.
4. `n = Integer.MAX_VALUE`.
5. `n = Integer.MIN_VALUE`.
6. Tất cả các bit đều là `1`.
7. Tất cả các bit đều là `0`.
8. Các bit luân phiên `101010...`.
9. `n` là luỹ thừa của 2.
10. `n` bằng `luỹ thừa của 2 - 1` (dạng `00001111...`).
11. `n` là một số chẵn có đúng 1 bit 1.
12. Mảng chứa toàn số 0.
13. Mảng chỉ chứa 1 phần tử.
14. Các phần tử trong mảng có cùng giá trị.
15. Truy vấn `K` lớn hơn số lượng bit tối đa (vd > 31).
16. Kích thước mảng đạt ngưỡng `10^5`.
17. Trích xuất bit trên một số âm rất lớn.
18. Xóa bit trên `Integer.MIN_VALUE` gây ra sai lệch số học.
19. Mảng chứa kết hợp số nguyên âm và dương.
20. Phép toán với số 64-bit thay vì 32-bit.
21. Đếm set bits của `Long.MIN_VALUE`.
22. Tìm bit 1 thấp nhất của số 0 (trả về 0).
23. Sử dụng `XOR` cho một số chẵn các số giống nhau (kết quả 0).
24. Sử dụng `XOR` cho một số lẻ các số giống nhau (kết quả là chính nó).
25. Bảng giá trị Trie quá lớn, gây Memory Limit Exceeded.
26. Yêu cầu tính XOR trong khoảng `[0, x]` với `x` rất lớn.
27. Đếm số lượng cặp XOR bằng một giá trị mục tiêu, khi không có cặp nào khớp.
28. Kích thước mask vượt quá 64.
29. Cây Trie chỉ có nhánh 0.
30. Cây Trie chỉ có nhánh 1.

## 13. Các biến thể của cấu trúc dữ liệu (Variants)
- **Trie 4-nhánh (Radix-4 Bitwise Trie)**: Gom nhóm 2 bit mỗi tầng để giảm nửa chiều cao cây (còn 16 tầng), tối ưu hơn về hằng số thời gian.
- **Persistent Bitwise Trie**: Sử dụng trong bài toán truy vấn Max XOR trên các khoảng `[L, R]` của một mảng.

## 14. So sánh với các cấu trúc dữ liệu khác (Comparisons)
- **Bitwise Trie vs HashSet**: HashSet có thể dùng để giải quyết Max XOR trong một số phiên bản (kiểm tra theo các tiền tố từ bit cao đến thấp bằng cách dùng mask), nhưng Trie cho thời gian tính ổn định và cung cấp khả năng tìm kiếm mở rộng dễ dàng hơn so với HashSet.

## 15. Các câu hỏi phỏng vấn (20 Interview Questions)
1. Hãy giải thích kỹ thuật Brian Kernighan và phân tích độ phức tạp thời gian.
2. Tại sao `n & (-n)` lại trả về bit 1 thấp nhất? Chứng minh bằng bù 2.
3. Cài đặt thuật toán tìm Max XOR của 2 phần tử trong mảng $O(N)$.
4. Làm thế nào để hoán đổi hai biến mà không dùng biến tạm?
5. Làm thế nào để kiểm tra một số có phải luỹ thừa của 4 không chỉ bằng bitwise?
6. Bạn có thể sử dụng Bitwise Trie để tìm Min XOR không? Nếu có, thì như thế nào?
7. Giải bài toán Single Number III (Tìm 2 số xuất hiện 1 lần trong mảng).
8. Làm thế nào để đếm số bit cần thay đổi để chuyển từ số A sang số B?
9. Viết hàm sinh tất cả các tập con của một tập n phần tử dùng Bitmask.
10. Tại sao người ta chuộng dùng Bit manipulation trong bài DP (Dynamic Programming) trên tập hợp?
11. Trình bày thuật toán đếm tổng khoảng cách Hamming giữa tất cả các cặp trong mảng.
12. Có thể tạo Bitwise Trie xử lý cả số âm không?
13. Bạn xử lý tràn số trong thao tác bit như thế nào?
14. Phân biệt biểu diễn dấu phẩy động và số nguyên. Có thể áp dụng Bitwise cho float được không?
15. Bit manipulation có thể tăng tốc độ thuật toán đồ thị như TSP (Traveling Salesperson Problem) như thế nào?
16. Tối ưu bộ nhớ Bitwise Trie bằng cách nào nếu số lượng số nạp vào là rất ít nhưng giá trị lớn?
17. Nêu cách chia một số cho 3 mà không dùng phép chia bằng các thao tác bit.
18. Tính XOR của tất cả các số từ 1 đến N trong $O(1)$.
19. Làm sao để xoá bit 1 cao nhất của một số?
20. Trình bày Gosper's Hack để sinh ra phần tử có $k$ bit 1 lớn tiếp theo.

## 16. Hướng dẫn giải quyết vấn đề (Problem-Solving Strategies)
- **Bài toán đếm số/vị trí bit**: Luôn luôn nghĩ tới việc xử lý độc lập trên từng vị trí bit (`i` chạy từ 0 đến 31). Tổng hợp kết quả sau đó.
- **Bài toán tìm Max/Min qua toán tử XOR/AND/OR**: Cân nhắc sử dụng Trie nếu liên quan đến tiền tố, hoặc cấu trúc HashMap kết hợp Bitmask.
- **Tối ưu vòng lặp trạng thái DP**: Dùng `n & (n-1)` để loại bỏ bit 1, duyệt qua tất cả các tập con của một mask qua vòng lặp `for(int sub = mask; sub > 0; sub = (sub - 1) & mask)`.

## 17. Các thư viện và công cụ (Libraries and Tools)
- `Integer.bitCount(int i)`, `Integer.lowestOneBit(int i)`, `Integer.highestOneBit(int i)` trong Java Core hỗ trợ sẵn và thường được tối ưu trực tiếp bằng mã máy (intrinsics).

## 18. Lịch sử và nguồn gốc (History and Origins)
- Brian Kernighan, người đồng phát minh ra C và AWK, đã phổ biến kỹ thuật `n & (n-1)` trong cuốn sách kinh điển "The C Programming Language". Tuy nhiên kỹ thuật này đã xuất hiện sớm hơn trong các tài liệu từ thập niên 1960 của Peter Wegner.

## 19. Tài liệu tham khảo (References)
- "Hacker's Delight" by Henry S. Warren Jr. - cuốn "kinh thánh" của Bit manipulation.
- Sách "The C Programming Language".

## 20. Bài tập thực hành (Practice Exercises)
- Tìm tập hợp tất cả các Submasks của một Mask.
- Xây dựng lớp Trie tùy chỉnh dùng mảng để tăng tốc giải thuật Max XOR.

## 21. Dự án nhỏ (Mini-projects)
- Viết một chương trình nén dữ liệu cơ bản (như Huffman Encoding) áp dụng mạnh mẽ việc điều khiển cấp độ bit.

## 22. Các tính năng ngôn ngữ liên quan (Language Features)
- Toán tử nhị phân (`&, |, ^, ~, <<, >>, >>>`) trong Java.
- Annotation `@HotSpotIntrinsicCandidate` trên các hàm của thư viện `java.lang.Integer` giúp biến chúng thành assembly.

## 23. Gỡ lỗi (Debugging Techniques)
- Thường xuyên in các số nhị phân bằng `Integer.toBinaryString()` kết hợp với việc độn thêm số 0 vào trước (padding) để nhìn rõ chuỗi bit nguyên vẹn 32 bit.

## 24. Tổng kết (Conclusion)
Kỹ thuật thao tác bit nâng cao không chỉ là một công cụ giúp cải thiện hằng số độ phức tạp mà còn đem lại cách tiếp cận mới để mô hình hóa trạng thái (qua Bitmask DP) hoặc giải quyết truy vấn (qua Trie). Đây là một nhóm kỹ năng cực kỳ sắc bén để làm chủ các buổi phỏng vấn khắt khe nhất.
