---
title: Rabin-Karp Algorithm
description: Complete guide to Rabin-Karp algorithm, rolling hash, hash collisions, and string matching techniques.
language: vi
---

# Rabin-Karp Algorithm

## 1. Metadata
- **Level**: Intermediate/Advanced
- **Topics**: String Matching, Hashing, Rolling Hash, Number Theory
- **Prerequisites**: Strings, Modular Arithmetic, Arrays
- **Time Complexity**: Average $\mathcal{O}(N + M)$, Worst $\mathcal{O}(N \times M)$
- **Space Complexity**: $\mathcal{O}(1)$

## 2. Purpose (Mục đích)
Thuật toán **Rabin-Karp** được sử dụng để tìm kiếm một mẫu (pattern) trong một chuỗi văn bản (text) bằng cách sử dụng **Hashing**. Thay vì so sánh từng ký tự một như thuật toán Naive, nó so sánh mã băm (hash value) của pattern với mã băm của từng substring trong text.

## 3. Motivation (Động lực)
Trong tìm kiếm chuỗi, việc dịch chuyển một ký tự và tính toán lại từ đầu sẽ tốn $\mathcal{O}(M)$ thời gian. Nếu ta có thể cập nhật mã băm của substring tiếp theo trong $\mathcal{O}(1)$ dựa trên substring trước đó, ta sẽ tiết kiệm được rất nhiều thời gian. Kỹ thuật này gọi là **Rolling Hash**, là cốt lõi của Rabin-Karp, giúp giải quyết hiệu quả bài toán Single Pattern Matching và đặc biệt là Multiple Pattern Matching (kết hợp với Bloom Filter hoặc Hash Set).

## 4. Mathematical Foundation (Nền tảng toán học)
Để xây dựng **Rolling Hash**, ta xem chuỗi như một số trong hệ cơ số $B$ (base).
- $H = (c_1 \cdot B^{M-1} + c_2 \cdot B^{M-2} + \dots + c_M \cdot B^0) \pmod Q$
Trong đó:
- $B$ thường là kích thước tập ký tự (ví dụ: 256 cho ASCII, hoặc 31, 29 cho chữ cái thường).
- $Q$ là một số nguyên tố lớn (**prime number**) để ngăn chặn **integer overflow** và giảm thiểu **hash collisions**.

Khi trượt cửa sổ sang phải một ký tự (bỏ $c_1$, thêm $c_{M+1}$):
- $H_{next} = ((H - c_1 \cdot B^{M-1}) \cdot B + c_{M+1}) \pmod Q$

Lưu ý: $(H - c_1 \cdot B^{M-1})$ có thể âm, cần cộng thêm $Q$ trước khi modulo.

## 5. Core Theory (Lý thuyết cốt lõi)
**Rabin-Karp** có ba phần chính:
1. **Hash Calculation**: Tính mã băm ban đầu cho pattern và substring đầu tiên của text.
2. **Rolling Hash**: Trượt cửa sổ trên text, tính mã băm mới trong $\mathcal{O}(1)$.
3. **Collision Handling**: Khi hash của pattern và substring bằng nhau, ta phải so sánh từng ký tự (character-by-character) để xác nhận (vì có thể xảy ra **hash collision**).

**Double Hashing** có thể được sử dụng với hai bộ $(B_1, Q_1)$ và $(B_2, Q_2)$ để giảm xác suất collision xuống mức không đáng kể, loại bỏ nhu cầu so sánh chuỗi trong thực tế.

## 6. Visual Explanation (Giải thích trực quan)
```mermaid
graph TD
    A[Text: "ABCCBA", Pattern: "CC"] --> B[Tính Hash "CC"]
    A --> C[Tính Hash "AB"]
    B --> D{Hash "CC" == Hash "AB"?}
    C --> D
    D -- Không --> E[Trượt cửa sổ: Xóa 'A', thêm 'C' -> "BC"]
    E --> F[Tính Rolling Hash "BC"]
    F --> G{Hash "CC" == Hash "BC"?}
    G -- Không --> H[Trượt cửa sổ: Xóa 'B', thêm 'C' -> "CC"]
    H --> I[Tính Rolling Hash "CC"]
    I --> J{Hash "CC" == Hash "CC"?}
    J -- Có --> K[So sánh chuỗi "CC" và "CC"]
    K -- Trùng khớp --> L[Trả về Index 2]
```

## 7. Java Implementation (Cài đặt Java)
```java
public class RabinKarp {
    public static int search(String pattern, String text) {
        int m = pattern.length();
        int n = text.length();
        if (m == 0 || n == 0 || m > n) return -1;

        int B = 256;
        int Q = 1000000007;
        long h = 1;
        for (int i = 0; i < m - 1; i++) {
            h = (h * B) % Q;
        }

        long pHash = 0;
        long tHash = 0;
        for (int i = 0; i < m; i++) {
            pHash = (B * pHash + pattern.charAt(i)) % Q;
            tHash = (B * tHash + text.charAt(i)) % Q;
        }

        for (int i = 0; i <= n - m; i++) {
            if (pHash == tHash) {
                boolean match = true;
                for (int j = 0; j < m; j++) {
                    if (text.charAt(i + j) != pattern.charAt(j)) {
                        match = false;
                        break;
                    }
                }
                if (match) return i;
            }
            if (i < n - m) {
                tHash = (B * (tHash - text.charAt(i) * h) + text.charAt(i + m)) % Q;
                if (tHash < 0) {
                    tHash = (tHash + Q);
                }
            }
        }
        return -1;
    }
}
```

## 8. Step-by-Step (Từng bước)
1. Kiểm tra điều kiện biên: Nếu `pattern` lớn hơn `text`, trả về -1.
2. Tính hệ số `h` = $B^{m-1} \pmod Q$.
3. Tính mã băm `pHash` cho `pattern` và `tHash` cho `m` ký tự đầu tiên của `text`.
4. Duyệt từ `i = 0` đến `n - m`:
   - Nếu `pHash == tHash`, so sánh từng ký tự để xử lý collision.
   - Nếu khớp hoàn toàn, trả về chỉ số `i`.
   - Nếu `i < n - m`, cập nhật `tHash` bằng Rolling Hash.
   - Xử lý số âm sau khi cập nhật mã băm.

## 9. Complexity Analysis (Phân tích độ phức tạp)
- **Time Complexity**:
  - Tốt nhất / Trung bình: $\mathcal{O}(N + M)$. Việc tính mã băm ban đầu tốn $\mathcal{O}(M)$, trượt cửa sổ tốn $\mathcal{O}(N)$. Do số lượng collision rất ít nhờ dùng số nguyên tố lớn, thời gian so sánh chuỗi là hằng số.
  - Xấu nhất (Worst case): $\mathcal{O}(N \times M)$. Xảy ra khi tất cả các substring đều có cùng mã băm (ví dụ pattern = "AAA", text = "AAAAAAA" hoặc hash collision liên tục).
- **Space Complexity**: $\mathcal{O}(1)$. Chỉ dùng một số biến nguyên để lưu hash.

## 10. JVM Analysis (Phân tích JVM)
- **CPU Cache**: Việc duyệt chuỗi theo thứ tự tuần tự tối ưu hóa Spatial Locality cho L1/L2 cache.
- **Branch Prediction**: Vòng lặp rolling hash ít rẽ nhánh, giúp CPU branch predictor hoạt động hiệu quả. Nhánh `pHash == tHash` rất hiếm khi được thực thi, do đó branch predictor thường dự đoán "not taken".
- **Garbage Collection**: Thuật toán không tạo ra các object `String` mới (như `substring()`), do đó Zero GC allocation.

## 11. OpenJDK Analysis (Phân tích OpenJDK)
Trong OpenJDK, hàm `String.indexOf` mặc định sử dụng thuật toán Naive hoặc Boyer-Moore (Intrinsic trong HotSpot). Rabin-Karp không được dùng trong `String.indexOf` do chi phí tính toán modulo khá tốn kém so với SIMD instructions cho char matching, nhưng nó được ứng dụng trong các bài toán nội bộ cần so sánh nhiều patterns cùng lúc.

## 12. Production Usage (Sử dụng trong thực tế)
- **Plagiarism Detection**: Hệ thống kiểm tra đạo văn (ví dụ MOSS) sử dụng Rabin-Karp với Multiple Patterns.
- **Rsync Protocol**: Thuật toán đồng bộ file dùng Rolling Hash (như rsync's adler-32) tương tự như Rabin-Karp để tìm block bị thay đổi một cách hiệu quả.
- **Intrusion Detection Systems (IDS)**: Tìm kiếm nhiều chữ ký (signatures) mã độc cùng lúc mà không tốn kém hiệu năng lặp lại.

## 13. Design Decisions (Các quyết định thiết kế)
- **Modulo Base ($B$)**: Chọn lớn hơn số lượng ký tự có thể có (ví dụ 256) để tránh collision nhỏ.
- **Prime Modulo ($Q$)**: Chọn số nguyên tố lớn (ví dụ $10^9 + 7$) để giảm xác suất collision xuống $1/Q$.
- **Double Hashing**: Dùng 2 cặp $(B_1, Q_1)$ và $(B_2, Q_2)$. Chỉ khi cả hai mã băm khớp thì mới so sánh, loại bỏ gần như 100% collision và không cần chuỗi thực để xác nhận.

## 14. Common Bugs (20 Common Bugs)
1. Quên `+ Q` khi `tHash < 0` trong rolling hash.
2. Modulo `h = Math.pow(B, M-1) % Q` sai (do overflow số thực/nguyên). Cần tính bằng vòng lặp.
3. Không modulo trong mỗi bước tính `h`.
4. Trượt cửa sổ sai số vòng lặp (off-by-one error ở `n-m`).
5. Dùng `Q` quá lớn (vượt quá giới hạn `long` khi nhân `tHash * B`).
6. Gọi `charAt()` thay vì dùng `byte[]` khi tối ưu hiệu năng làm overhead cao.
7. Bỏ qua bước so sánh từng ký tự (exact match) dẫn đến false positive.
8. So sánh chuỗi sai ở phần bù vòng lặp (vòng lặp `j` đến `m` thay vì bắt đầu đúng index).
9. NullPointerException khi pattern hoặc text là null.
10. Lỗi xử lý pattern dài hơn text.
11. Không dùng số nguyên tố cho `Q`, làm hash phân phối không đều.
12. Chọn `B` nhỏ hơn bảng chữ cái (ví dụ B=26 nhưng có ký tự đặc biệt).
13. Nhầm lẫn giữa biến `i` và `j` khi so sánh ký tự tại `i+j`.
14. Không xử lý empty pattern (`""`).
15. Không xử lý overflow ở công thức `B * pHash`. Nên dùng `long`.
16. Dùng toán tử bitwise sai thay cho modulo trong Rabin-Karp thuần túy.
17. Khởi tạo `h` bằng 0 thay vì 1.
18. Không tính đúng chỉ số của ký tự bị loại bỏ và thêm vào.
19. Không dùng Rolling hash mà tính lại hash từ đầu ở mỗi index $\mathcal{O}(N \times M)$.
20. Trả về true ngay khi check hash collision thành công mà không cập nhật index tìm thấy.

## 15. Edge Cases (30 Edge Cases)
1. Text rỗng, Pattern không rỗng.
2. Pattern rỗng, Text không rỗng.
3. Text và Pattern đều rỗng.
4. Text ngắn hơn Pattern.
5. Text dài bằng Pattern và khớp hoàn toàn.
6. Text dài bằng Pattern và không khớp.
7. Khớp ở vị trí đầu tiên của text.
8. Khớp ở vị trí cuối cùng của text.
9. Khớp ở giữa text.
10. Toàn bộ text là một ký tự lặp đi lặp lại.
11. Toàn bộ pattern là một ký tự lặp đi lặp lại.
12. Pattern xuất hiện nhiều lần (overlap).
13. Pattern xuất hiện nhiều lần (không overlap).
14. Ký tự không nằm trong bảng chữ cái ASCII chuẩn (ví dụ Unicode).
15. Text rất dài (hàng triệu ký tự).
16. Pattern rất dài (hàng ngàn ký tự).
17. Rolling hash kết quả âm nhiều lần liên tục.
18. `B` lớn dẫn đến overflow khi nhân với `h`.
19. `Q` là số nguyên tố lớn gần Max Long.
20. Ký tự bị xóa có mã ASCII là 0.
21. Ký tự thêm vào có mã ASCII là 0.
22. Pattern và text chứa escape characters (`\n`, `\t`).
23. String chứa các khoảng trắng xen kẽ.
24. Pattern chỉ khác 1 ký tự cuối ở mỗi chuỗi con.
25. Text gồm 2 phần nối lại giống hệt pattern.
26. Mảng text không kết thúc bằng null character.
27. Đệ quy hash quá sâu nếu implement theo kiểu functional.
28. Pattern có hash bằng đúng `0`.
29. Giá trị `pHash` và `tHash` sau cùng bằng nhau ở bước lặp cuối `n-m`.
30. Hash trùng lặp với xác suất siêu hiếm (Worst Case Generator).

## 16. Optimization (Tối ưu hóa)
- **Double Hashing**: Dùng 2 giá trị modulo độc lập $Q_1, Q_2$. Nếu cả hai khớp thì coi như chính xác 100%, bỏ qua kiểm tra `equals()`.
- **Bitwise Modulo**: Nếu chọn `Q` là số nguyên tố Mersenne (ví dụ $2^{31}-1$), ta có thể tính modulo bằng phép tính bit nhanh hơn `&` và `>>`.
- **SIMD / Vector API**: Sử dụng Java Vector API để kiểm tra so sánh chuỗi nhanh sau khi phát hiện collision.

## 17. Best Practices (Thực hành tốt nhất)
- Dùng `long` cho tính toán hash để tránh tràn số.
- Luôn kiểm tra chuỗi rỗng và so sánh độ dài `text` với `pattern`.
- Tách riêng hàm tính hash ban đầu và rolling hash để clean code.
- Nên dùng bộ khởi tạo biến `B` và `Q` hằng số tĩnh (`static final`).

## 18. Benchmark (Đánh giá hiệu năng)
- **Naive $\mathcal{O}(N \times M)$**: Text 10MB, Pattern 1000 kí tự $\approx 1500$ ms.
- **Rabin-Karp $\mathcal{O}(N + M)$**: Text 10MB, Pattern 1000 kí tự $\approx 25$ ms.
- **KMP**: Text 10MB $\approx 15$ ms (KMP nhanh hơn do không tốn chi phí phép `%`).
- **Rabin-Karp Multi-pattern**: Vượt trội hoàn toàn so với KMP khi phải tìm $K$ pattern cùng lúc.

## 19. Unit Testing (Kiểm thử)
```java
@Test
void testRabinKarp() {
    assertEquals(0, RabinKarp.search("A", "A"));
    assertEquals(-1, RabinKarp.search("B", "A"));
    assertEquals(2, RabinKarp.search("ABC", "XXABCYY"));
    assertEquals(0, RabinKarp.search("A", "AAAA")); // first occurrence
    assertEquals(-1, RabinKarp.search("LONG", "SHORT"));
}
```

## 20. Interview Questions (20 Interview Questions)
1. Rolling Hash là gì và tại sao nó hữu ích?
2. Sự khác biệt giữa Rabin-Karp và KMP?
3. Tại sao chọn $Q$ là một số nguyên tố lớn?
4. Điều gì xảy ra nếu $Q$ không phải là số nguyên tố?
5. Làm sao để xử lý giá trị hash bị âm trong quá trình dịch cửa sổ?
6. Độ phức tạp Worst-case của Rabin-Karp và khi nào nó xảy ra?
7. Cải thiện Worst-case của Rabin-Karp như thế nào?
8. Spurious Hit (Hash Collision) là gì?
9. Double Hashing giúp giải quyết vấn đề gì trong thuật toán này?
10. Tại sao phải tính $h = B^{M-1} \pmod Q$?
11. Rabin-Karp có thể dùng để tìm nhiều pattern (Multiple Pattern Matching) không?
12. Hãy trình bày cách ứng dụng thuật toán này vào Plagiarism Detection.
13. Tại sao toán tử modulo trong Java lại tốn kém?
14. Có thể dùng Rabin-Karp cho ma trận 2D được không? (Trả lời: Có, thuật toán Baker-Bird).
15. Làm thế nào để implement Rolling Hash với Bitwise Shift?
16. Nếu bảng chữ cái $B=256$, giá trị của $B$ có ảnh hưởng thế nào đến overflow?
17. Phân tích không gian bộ nhớ của Rabin-Karp.
18. Sự khác biệt giữa `String.hashCode()` trong Java và Rolling Hash?
19. Giải thích thuật toán tìm Longest Palindromic Substring dùng Rolling Hash.
20. Tại sao Rabin-Karp ít được dùng trong hàm có sẵn của hệ điều hành/ngôn ngữ (như strstr)?

## 21. Practice Problems Link (Liên kết bài tập)
- [LeetCode 187: Repeated DNA Sequences](https://leetcode.com/problems/repeated-dna-sequences/)
- [LeetCode 1044: Longest Duplicate Substring](https://leetcode.com/problems/longest-duplicate-substring/)
- [LeetCode 28: Find the Index of the First Occurrence in a String](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/)

## 22. Pattern Recognition (Nhận diện Pattern)
- "Tìm kiếm chuỗi con" + "So sánh liên tục các đoạn cùng độ dài".
- "Tìm chuỗi lặp lại" hoặc "Tìm đoạn chung lớn nhất" (kết hợp với Binary Search).
- Khi có thay đổi / dịch chuyển 1 phần tử trên cửa sổ kích thước cố định, sử dụng **Rolling Hash**.

## 23. Real Case Study (Nghiên cứu tình huống thực tế)
Hệ thống **MOSS (Measure of Software Similarity)** dùng phương pháp **Winnowing**, một mở rộng của Rolling Hash (Rabin-Karp) để tạo ra các k-grams (các chuỗi độ dài k). Hash của các k-grams được chọn lọc và so sánh để tìm mức độ giống nhau giữa hai source code, bỏ qua các ký tự không quan trọng như khoảng trắng và comment.

## 24. Summary, Checklist (Tóm tắt, Checklist)
- [x] Hiểu nguyên lý của Rolling Hash và công thức dịch vòng.
- [x] Nắm rõ cách tính $h$, cập nhật $tHash$, xử lý số âm.
- [x] Biết cách xử lý Spurious Hit (Collision) bằng loop so sánh char.
- [x] Hiểu ưu điểm của việc sử dụng Double Hashing.
- [x] Nhận diện bài toán cần Rabin-Karp (đặc biệt là Multiple Pattern hoặc Binary Search Text).
