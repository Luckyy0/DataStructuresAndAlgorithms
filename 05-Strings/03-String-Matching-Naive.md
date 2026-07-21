# 1. Metadata
- **Title**: Naive String Matching
- **Topic**: Data Structures and Algorithms - Strings
- **Language**: Java 21
- **Difficulty**: Basic
- **Target Audience**: Software Engineers, Technical Interview Candidates

# 2. Purpose (Mục đích)
Thuật toán Naive String Matching (Tìm kiếm chuỗi nguyên thủy/vét cạn) nhằm mục đích tìm kiếm tất cả hoặc vị trí đầu tiên của một chuỗi mẫu (pattern) bên trong một văn bản (text). Đây là thuật toán cơ sở nhất để làm quen với các khái niệm xử lý chuỗi và sliding window (cửa sổ trượt).

# 3. Motivation (Động lực)
Trong xử lý văn bản, nhu cầu tìm kiếm một từ khóa hoặc một chuỗi con bên trong một văn bản lớn là cực kỳ phổ biến (ví dụ: `Ctrl + F` trong trình duyệt). Mặc dù các thuật toán tối ưu hơn (như KMP, Rabin-Karp) tồn tại, Naive String Matching vẫn quan trọng vì tính đơn giản, dễ cài đặt và thường chạy rất nhanh trên thực tế đối với các chuỗi mẫu ngắn.

# 4. Mathematical Foundation (Nền tảng toán học)
Giả sử văn bản là một mảng ký tự $T[0..N-1]$ có độ dài $N$ và chuỗi mẫu là $P[0..M-1]$ có độ dài $M$.
Thuật toán so sánh $P$ với từng mảng con $T[i..i+M-1]$ của văn bản, trong đó $0 \le i \le N-M$.
Số phép thử tối đa là $N - M + 1$. Mỗi lần thử yêu cầu tối đa $M$ phép so sánh ký tự. Do đó, số lượng phép so sánh tối đa là $(N - M + 1) \times M$.

# 5. Core Theory (Lý thuyết cốt lõi)
Thuật toán sử dụng một "Sliding Window" độ dài $M$ trượt qua văn bản độ dài $N$. Tại mỗi vị trí $i$ (từ $0$ đến $N-M$), thuật toán khớp từng ký tự của pattern với văn bản. Nếu tất cả $M$ ký tự khớp, ta tìm thấy pattern tại vị trí $i$. Nếu có bất kỳ sự bất đồng (mismatch) nào, ta trượt pattern sang phải 1 vị trí ($i \to i+1$) và bắt đầu so sánh lại từ đầu.

# 6. Visual Explanation (Giải thích trực quan)
```mermaid
flowchart TD
    A[Bắt đầu: i = 0] --> B{i <= N - M?}
    B -- Có --> C[j = 0]
    C --> D{j < M và T[i+j] == P[j]?}
    D -- Có --> E[j = j + 1]
    E --> D
    D -- Không --> F{j == M?}
    F -- Có --> G[Lưu vị trí i là kết quả]
    G --> H[i = i + 1]
    F -- Không --> H
    H --> B
    B -- Không --> I[Kết thúc thuật toán]
```

# 7. Java Implementation (Mã nguồn Java)
```java
public class NaiveStringMatching {
    /**
     * Finds the first occurrence of a pattern in a text.
     * @param text The text to search in.
     * @param pattern The pattern to find.
     * @return The starting index of the first match, or -1 if not found.
     */
    public static int indexOf(String text, String pattern) {
        if (text == null || pattern == null) return -1;
        
        int n = text.length();
        int m = pattern.length();
        
        if (m == 0) return 0; // Empty pattern matches at index 0
        if (m > n) return -1; // Pattern longer than text cannot match
        
        // Trượt cửa sổ qua từng vị trí khả thi
        for (int i = 0; i <= n - m; i++) {
            int j = 0;
            // So sánh từng ký tự trong pattern
            while (j < m && text.charAt(i + j) == pattern.charAt(j)) {
                j++;
            }
            // Nếu so sánh hết chiều dài pattern, tức là đã khớp hoàn toàn
            if (j == m) {
                return i;
            }
        }
        
        return -1;
    }
}
```

# 8. Step-by-Step (Từng bước một)
1. **Khởi tạo**: Lấy $N$ (chiều dài Text) và $M$ (chiều dài Pattern).
2. **Kiểm tra biên**: Nếu pattern rỗng, trả về 0. Nếu $M > N$, trả về -1.
3. **Vòng lặp ngoài**: Chạy biến $i$ từ $0$ đến $N - M$. $i$ là vị trí bắt đầu của cửa sổ trong Text.
4. **Vòng lặp trong**: Chạy biến $j$ từ $0$ đến $M-1$.
5. **So sánh**: Kiểm tra `text.charAt(i + j) == pattern.charAt(j)`. Nếu khớp, tăng $j$. Nếu không khớp, thoát vòng lặp trong.
6. **Xác nhận**: Sau khi kết thúc vòng lặp trong, nếu $j == M$, ta đã tìm thấy kết quả.

# 9. Complexity Analysis (Phân tích độ phức tạp)
- **Time Complexity (Độ phức tạp thời gian)**: 
  - **Worst Case**: $O(N \times M)$. Xảy ra khi văn bản và mẫu có các ký tự trùng lặp nhau lặp đi lặp lại (ví dụ: Text = "AAAAAAA", Pattern = "AAAB").
  - **Best Case**: $O(N)$. Xảy ra khi sự bất đồng ký tự xuất hiện ngay tại ký tự đầu tiên của pattern (ví dụ: Text = "ABCDEFG", Pattern = "X").
- **Space Complexity (Độ phức tạp không gian)**: $O(1)$ vì chúng ta không sử dụng thêm bộ nhớ phụ thuộc vào kích thước dữ liệu, chỉ sử dụng một vài biến con trỏ (pointers).

# 10. JVM Analysis (Phân tích JVM)
Trong Java 21, đối tượng `String` sử dụng `byte[]` làm backend (Compact Strings). Việc gọi `charAt()` sẽ được JVM biên dịch JIT (Just-In-Time) thành thao tác truy xuất trực tiếp vào mảng byte hoặc char. JVM thường loại bỏ bound checking (kiểm tra ranh giới mảng) trong các vòng lặp `for` có điều kiện rõ ràng, giúp tăng hiệu năng so sánh đáng kể so với việc sử dụng hàm lớp ngoài.

# 11. OpenJDK Analysis (Phân tích OpenJDK)
Trong OpenJDK, hàm `String.indexOf(String str)` truyền thống từng sử dụng Brute-force/Naive matching cho các chuỗi có độ dài ngắn. Gần đây (sau Java 9), Java sử dụng Intrinsics (các hàm nội tại của JVM được viết bằng Assembly, cụ thể là các lệnh SIMD hoặc AVX trên kiến trúc x86) để tối ưu hoá phép so sánh chuỗi, thực hiện khớp đa ký tự cùng một lúc, làm cho hàm chuẩn của Java cực kỳ tối ưu, vượt xa các thuật toán thuần túy như Naive hay thậm chí KMP đối với chuỗi thông thường.

# 12. Production Usage (Sử dụng trong thực tế)
Thuật toán Naive thường được dùng:
- Làm Fallback (phương án dự phòng) khi chuỗi mẫu cực kỳ nhỏ (VD: $M \le 3$), vì chi phí khởi tạo bảng (như LPS của KMP hay Hash của Rabin-Karp) lớn hơn cả việc duyệt trực tiếp.
- Làm module học tập để giới thiệu cơ chế Sliding Window và Pattern Matching.
- Ở các ngôn ngữ cấp thấp, khi kết hợp với Loop Unrolling.

# 13. Design Decisions (Các quyết định thiết kế)
Việc sử dụng 2 vòng lặp (nested loops) thay vì các hàm thư viện cho phép kiểm soát tốt hơn đối với quá trình so sánh. Thiết kế dừng sớm (`break` khi không khớp) là một quyết định quan trọng nhằm đưa thời gian thực thi trung bình về gần với $O(N)$ hơn. Quyết định không sử dụng thêm mảng phụ giúp đảm bảo Space Complexity luôn là $O(1)$.

# 14. Common Bugs (20 Lỗi thường gặp)
1. **Off-by-one error (Lỗi lệch 1)**: Vòng lặp ngoài sử dụng `i < n - m` thay vì `i <= n - m`, bỏ sót vị trí cuối cùng.
2. **Missing variables update**: Quên không tăng con trỏ vòng lặp.
3. **NullPointerException**: Không kiểm tra `text` hoặc `pattern` bị `null`.
4. **Out of bounds exception**: Truy cập `text.charAt(i + j)` khi `i + j` vượt quá ranh giới.
5. **Pattern longer than Text**: Không kiểm tra `M > N`, dẫn đến lỗi logic hoặc tốn kém tính toán.
6. **Inner loop scope issue**: Đặt `j` bên trong vòng `while` khiến không thể check `j == M` sau đó.
7. **Empty Pattern mismatch**: Trả về `-1` thay vì `0` khi mẫu rỗng.
8. **Char comparison overhead**: Lặp lại `charAt` nhiều lần thay vì đưa vào một biến tạm.
9. **Incorrect loop resetting**: Cố gắng điều chỉnh vị trí `i` bên trong vòng lặp thay vì để `i++` tự động.
10. **Case Sensitivity ignorance**: Bỏ quên khác biệt chữ hoa, chữ thường khi đề bài yêu cầu "case-insensitive".
11. **Confusion between M and N**: Đảo ngược điều kiện độ dài.
12. **Continuing after first match**: Không dừng lại sau lần khớp đầu tiên dù đề bài yêu cầu `indexOf`.
13. **Array vs String Indexing**: Sử dụng cú pháp `[]` thay vì `.charAt()`.
14. **Overlapping checks**: Bỏ qua các chuỗi con chồng chéo (overlapping) khi đếm tất cả kết quả.
15. **Integer overflow**: Khó xảy ra với chuỗi thông thường nhưng `length()` có thể đạt giới hạn lớn.
16. **Suboptimal exit condition**: Cố gắng so sánh thêm kể cả khi lượng ký tự còn lại không đủ `M`.
17. **Using == on strings**: Nhầm lẫn `text == pattern` thay vì từng ký tự.
18. **Not handling Whitespaces**: Quên không xử lý khoảng trắng theo yêu cầu đầu vào.
19. **Improper Unicode handling**: Dùng `charAt` với Surrogate Pairs sẽ gây sai số.
20. **Shadowing variables**: Đặt tên biến lặp ở ngoài và ở trong vòng lặp giống nhau.

# 15. Edge Cases (30 Trường hợp ngoại lệ)
1. Pattern là chuỗi rỗng (`""`).
2. Text là chuỗi rỗng (`""`).
3. Cả Pattern và Text đều là chuỗi rỗng.
4. Pattern dài hơn Text.
5. Pattern và Text bằng nhau hoàn toàn.
6. Pattern và Text có độ dài bằng nhau nhưng khác một ký tự cuối.
7. Text chỉ chứa một ký tự.
8. Pattern chỉ chứa một ký tự.
9. Pattern nằm ở ngay đầu Text.
10. Pattern nằm ở ngay cuối Text.
11. Text có chứa các chuỗi con chồng chéo (ví dụ "AAAA" tìm "AA").
12. Text chứa các ký tự Unicode/Emoji (yêu cầu xử lý Surrogate Pairs).
13. Text rất dài và chỉ khớp ở vị trí cuối cùng (Worst case cho break sớm).
14. Cả Text và Pattern bao gồm hoàn toàn cùng 1 ký tự nhưng Pattern ngắn hơn (VD: "AAA", "AA").
15. Text và Pattern có khác biệt về kiểu chữ (Upper/Lower case).
16. Text chứa ký tự ngắt dòng `\n` hoặc `\t`.
17. Pattern có khoảng trắng ở đầu hoặc cuối.
18. Pattern khớp tất cả ngoại trừ ký tự cuối cùng trong mỗi lần thử.
19. Pattern không tồn tại trong Text.
20. Text chứa các ký tự Null (`\0`).
21. Text chứa dữ liệu không phải tiếng Anh (Vietnamese, Chinese...).
22. Khớp chuỗi có chứa Escape characters như `\\`.
23. Khớp một pattern lập lại nhiều lần liền kề trong văn bản.
24. Pattern khớp một phần ở cuối văn bản nhưng bị cắt cụt (không đủ độ dài M).
25. Văn bản có kích thước vô cùng lớn (gần ngưỡng `Integer.MAX_VALUE`).
26. Mẫu có ký tự đặc biệt lặp lại, khiến cho việc tối ưu hoá break sớm ít có hiệu quả.
27. Chuỗi chứa các ký tự có dấu (VD: "á" vs "a" + dấu).
28. Pattern chỉ khớp khi bỏ qua các dấu chấm câu (Tuỳ thuộc vào logic biến thể).
29. Gọi `indexOf` với một chuỗi rỗng lên một chuỗi rỗng (Kết quả phải là 0).
30. Dùng các chữ cái latin giống nhau về hình thái nhưng khác mã unicode (ví dụ Cyrillic 'a' và Latin 'a').

# 16. Optimization (Tối ưu hóa)
- Mặc dù thuật toán nguyên bản là $O(N \times M)$, trong Java, ta có thể tối ưu bằng cách tìm nhanh ký tự đầu tiên của pattern `text.indexOf(pattern.charAt(0), i)` trước khi bắt đầu vòng lặp trong (gọi là Fast-Search hoặc Two-Way String Matching). Điều này giúp cải thiện đáng kể tốc độ trung bình vì các ký tự đầu thường phân bố đều.

# 17. Best Practices (Thực hành tốt nhất)
- Hạn chế tự cài đặt string matching thủ công trên môi trường Production trừ khi có yêu cầu đặc thù. Hãy sử dụng thư viện chuẩn `String.indexOf()` hoặc `String.contains()`.
- Luôn kiểm tra `null` và chuỗi rỗng ngay từ đầu hàm.
- Bắt đầu với thuật toán Naive trong các bài phỏng vấn để đảm bảo logic chạy đúng, sau đó đề cập đến KMP, Rabin-Karp như một giải pháp tối ưu hơn.

# 18. Benchmark (Đánh giá hiệu năng)
- **Naive Algorithm**: Trung bình chạy rất nhanh với tiếng Anh chuẩn $O(N)$, khoảng $0.5 - 2$ ms cho văn bản kích thước $100$ KB. Trong Worst-Case, nó có thể tốn $100+$ ms do phải loop nhiều lần.
- **KMP**: Thời gian luôn ổn định quanh $1-2$ ms cho mọi trường hợp với $100$ KB text.
- **Java `indexOf`**: < $0.1$ ms nhờ SIMD Intrinsics từ JVM.

# 19. Unit Testing (Kiểm thử đơn vị)
```java
@Test
void testNaiveStringMatching() {
    assertEquals(0, NaiveStringMatching.indexOf("hello", "he"));
    assertEquals(2, NaiveStringMatching.indexOf("hello", "ll"));
    assertEquals(-1, NaiveStringMatching.indexOf("hello", "x"));
    assertEquals(0, NaiveStringMatching.indexOf("hello", ""));
    assertEquals(-1, NaiveStringMatching.indexOf("hello", "hello world"));
    assertEquals(4, NaiveStringMatching.indexOf("AAAAAB", "AB"));
}
```

# 20. Interview Questions (20 Câu hỏi phỏng vấn)
1. **Câu hỏi**: Độ phức tạp tồi tệ nhất của Naive String Matching là gì và khi nào nó xảy ra?
2. **Câu hỏi**: Làm thế nào để thuật toán hoạt động nhanh hơn trong trường hợp văn bản thông thường?
3. **Câu hỏi**: Tại sao độ phức tạp không gian (Space Complexity) lại là $O(1)$?
4. **Câu hỏi**: Tại sao `String.indexOf()` trong Java lại nhanh hơn việc tự cài đặt thuật toán Naive?
5. **Câu hỏi**: Thuật toán Naive khác với KMP ở điểm cốt lõi nào?
6. **Câu hỏi**: Viết code đếm TẤT CẢ số lần xuất hiện của chuỗi con bằng Naive Algorithm.
7. **Câu hỏi**: Khi nào bạn nên chọn Naive thay vì Rabin-Karp?
8. **Câu hỏi**: Xử lý chuỗi Unicode có Emoji bằng thuật toán này thế nào trong Java?
9. **Câu hỏi**: Viết phiên bản đệ quy (recursive) cho Naive String Matching.
10. **Câu hỏi**: Tại sao vòng lặp ngoài lại chạy đến `N - M` thay vì `N`?
11. **Câu hỏi**: Làm thế nào để xử lý việc tìm kiếm Case-Insensitive bằng thuật toán này?
12. **Câu hỏi**: Lỗi lệch 1 (Off-by-one) phổ biến nhất trong thuật toán này là gì?
13. **Câu hỏi**: Đánh giá hiệu suất của Naive khi chuỗi Pattern là một ký tự duy nhất.
14. **Câu hỏi**: Làm thế nào để mở rộng thuật toán để hỗ trợ ký tự đại diện (Wildcards như `*` hoặc `?`)?
15. **Câu hỏi**: Thay vì sử dụng vòng lặp trong, việc gọi `text.substring().equals()` có tốt không? Tại sao?
16. **Câu hỏi**: Làm thế nào thuật toán này đối phó với chuỗi có hàng triệu ký tự?
17. **Câu hỏi**: Tính chất "Sliding Window" được áp dụng như thế nào trong giải pháp này?
18. **Câu hỏi**: Tại sao cần kiểm tra null hoặc độ dài pattern sớm?
19. **Câu hỏi**: Đưa ra một biến thể của Naive Matching bắt đầu khớp từ ký tự cuối của Pattern ngược về đầu (giống Boyer-Moore).
20. **Câu hỏi**: Kịch bản thực tế nào trong kỹ thuật phần mềm cần triển khai thuật toán này một cách thủ công?

# 21. Practice Problems Link (Liên kết bài tập thực hành)
Tham khảo file `03-String-Matching-Naive-Problems.md` trong cùng thư mục để thực hành 30 bài tập về chuyên đề này, từ mức độ LeetCode Easy đến Hard.

# 22. Pattern Recognition (Nhận diện khuôn mẫu)
- **Dấu hiệu**: "Find the index", "Check if substring", "Count occurrences of string".
- **Giải pháp**: Nếu kích thước chuỗi nhỏ hoặc khi được yêu cầu implement hàm gốc rễ nhất -> Sử dụng 2 vòng lặp (Sliding Window cố định độ dài).

# 23. Real Case Study (Nghiên cứu tình huống thực tế)
Trong các thư viện Logger nhỏ hoặc hệ thống nhúng (Embedded systems) dùng C/C++ có không gian RAM vô cùng chật hẹp, họ không thể sử dụng bộ nhớ phụ (như mảng LPS của KMP) để tra cứu chuỗi cấu hình. Do đó, Naive String matching thường là lựa chọn số 1 nhờ đặc điểm $O(1)$ memory và hiệu suất tốt trên các file log hoặc tín hiệu ngắn.

# 24. Summary & Checklist (Tóm tắt và Danh sách kiểm tra)
- [x] Hiểu cơ chế cửa sổ trượt (Sliding Window) tĩnh độ dài M.
- [x] Nhớ ranh giới vòng lặp ngoài là `N - M`.
- [x] Biết cách kiểm tra và bẻ khóa (break) sớm trong vòng lặp trong để tối ưu $O(N)$ Best case.
- [x] Nắm rõ Worst case xảy ra khi có các ký tự giống nhau tuần hoàn ($O(N \times M)$).
- [x] Hiểu rằng API ngôn ngữ như Java `indexOf` luôn vượt trội vì được biên dịch nội tại (Intrinsic) với SIMD.
