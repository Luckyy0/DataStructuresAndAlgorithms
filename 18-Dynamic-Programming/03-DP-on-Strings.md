# Dynamic Programming on Strings (Quy hoạch động trên chuỗi)

## 1. Giới thiệu chung (Overview)
Dynamic Programming (DP) on Strings là một nhóm các bài toán kinh điển trong Khoa học máy tính, chuyên giải quyết các vấn đề liên quan đến việc so sánh, biến đổi, hoặc tìm kiếm đặc trưng chung giữa hai hay nhiều chuỗi (strings).

## 2. Mục tiêu học tập (Learning Objectives)
- Nắm vững khái niệm cơ bản về Subsequence và Substring.
- Hiểu và áp dụng các công thức truy hồi (Recurrence Relations) cho chuỗi.
- Nhận diện các dạng toán phổ biến: LCS, Edit Distance, Palindrome.
- Áp dụng các kỹ thuật tối ưu hóa không gian bộ nhớ (Space Optimization).

## 3. Ứng dụng thực tế (Real-world Applications)
- **Tin sinh học (Bioinformatics):** Căn chỉnh trình tự DNA/RNA (Sequence alignment).
- **Hệ thống gợi ý:** Kiểm tra chính tả (Spell check) qua Edit Distance.
- **Diff tools:** Các công cụ kiểm tra sự thay đổi phiên bản mã nguồn như Git (lệnh git diff).
- **Trình biên dịch:** Nhận dạng mẫu thông qua Regex và Wildcard Matching.

## 4. Định nghĩa và Khái niệm cốt lõi (Core Concepts & Definitions)
- **String:** Chuỗi các ký tự nối tiếp nhau.
- **State (Trạng thái):** Thường biểu diễn bằng `dp[i][j]` là kết quả bài toán cho tiền tố `s1[0..i-1]` và `s2[0..j-1]`.
- **Transition (Chuyển trạng thái):** Cách tính `dp[i][j]` từ các trạng thái trước đó.

## 5. Substring vs Subsequence (Chuỗi con liên tiếp vs Dãy con)
- **Substring (Chuỗi con liên tiếp):** Là một tập hợp các ký tự liền kề nhau trong chuỗi gốc. VD: "abc" là substring của "abcde".
- **Subsequence (Dãy con):** Được tạo ra bằng cách xóa một số (hoặc không xóa) ký tự từ chuỗi gốc mà không làm thay đổi thứ tự các ký tự còn lại. VD: "ace" là subsequence của "abcde".

## 6. Longest Common Subsequence (LCS) Concept
LCS tìm dãy con chung dài nhất giữa hai chuỗi. Khác với substring, các ký tự không cần đứng liền nhau.
Công thức cơ bản:
- Nếu `s1[i-1] == s2[j-1]`: `dp[i][j] = dp[i-1][j-1] + 1`
- Nếu `s1[i-1] != s2[j-1]`: `dp[i][j] = max(dp[i-1][j], dp[i][j-1])`

## 7. Palindromes Concept (Khái niệm chuỗi đối xứng)
Palindrome là chuỗi đọc xuôi hay ngược đều giống nhau. Bài toán DP về Palindrome thường xử lý bằng cách:
- Đảo ngược chuỗi và biến bài toán về LCS.
- Mở rộng từ tâm (Expand from center) hoặc dùng mảng DP 2D `dp[i][j]` đại diện cho đoạn từ `i` đến `j` có phải là Palindrome không.

## 8. String Matching, Sequence Comparisons (Khớp chuỗi và So sánh chuỗi)
Các bài toán liên quan đến việc khớp (Matching) một chuỗi text với một chuỗi pattern (chứa wildcard `*`, `?` hoặc Regex).
Sequence comparison cũng bao gồm bài toán biến đổi chuỗi này thành chuỗi kia bằng ít thao tác nhất (Edit Distance).

## 9. Phân loại bài toán (Problem Classification)
1. **LCS-based:** Tìm chuỗi con/dãy con chung (Longest Common Subsequence, Longest Common Substring).
2. **Palindrome-based:** Tìm/đếm Palindrome (Longest Palindromic Subsequence).
3. **Edit Distance / Transformation:** Chi phí biến đổi nhỏ nhất (Levenshtein Distance).
4. **Pattern Matching:** Khớp chuỗi tổng quát (Regex, Wildcard Matching).

## 10. Nguyên lý Quy hoạch động trên chuỗi (DP Principles on Strings)
- **Optimal Substructure:** Cấu trúc con tối ưu, bài toán lớn phụ thuộc vào bài toán nhỏ ở các tiền tố (prefixes) của chuỗi.
- **Overlapping Subproblems:** Các trạng thái `dp[i][j]` có thể được gọi lại nhiều lần.

## 11. Xây dựng công thức truy hồi (Building State Transitions)
Quan sát ký tự cuối cùng của 2 tiền tố:
- Khi ký tự khớp (match): Tiến cả 2 con trỏ (`i-1`, `j-1`).
- Khi không khớp: Xét các trường hợp thêm (insert), xóa (delete), thay thế (replace).

## 12. Khởi tạo mảng (Initialization)
- Tiền tố rỗng: Khởi tạo hàng `0` và cột `0` cho ma trận `dp`.
- Ví dụ Edit Distance: Biến chuỗi độ dài `i` thành chuỗi rỗng cần `i` thao tác xóa, nên `dp[i][0] = i`.

## 13. Kỹ thuật Tối ưu không gian (Space Optimization)
Do `dp[i][j]` thường chỉ phụ thuộc vào `dp[i-1]` (hàng trước đó), ta có thể nén mảng DP 2D xuống mảng 1D.
- Khai báo: `dp = new int[n+1]`
- Cập nhật: Cần giữ biến `prev` để lưu giá trị `dp[i-1][j-1]` bị đè (overwritten).

## 14. Độ phức tạp (Time and Space Complexity)
- **Time Complexity:** Đa số các bài toán có O(M * N) với M, N là độ dài hai chuỗi.
- **Space Complexity:** O(M * N) không tối ưu, và O(min(M, N)) nếu áp dụng tối ưu mảng 1D.

## 15. Hướng dẫn Triển khai (Implementation Guide)
- Xác định rõ `dp[i][j]` mang ý nghĩa gì.
- Kiểm tra Base cases cẩn thận.
- Quản lý vòng lặp for: `i` từ 1 -> M, `j` từ 1 -> N.

## 16. Code Templates (Mẫu mã nguồn)
Template cơ bản DP trên 2 chuỗi (LCS form):
```java
int[][] dp = new int[m + 1][n + 1];
for (int i = 1; i <= m; i++) {
    for (int j = 1; j <= n; j++) {
        if (s1.charAt(i - 1) == s2.charAt(j - 1)) {
            dp[i][j] = dp[i - 1][j - 1] + 1; // Hoặc cost tương ứng
        } else {
            dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]); // Tùy bài toán
        }
    }
}
```

## 17. 30 Edge Cases (Các trường hợp biên)
1. Cả hai chuỗi đều rỗng (`""` vs `""`).
2. Chuỗi thứ nhất rỗng, chuỗi thứ hai không rỗng.
3. Chuỗi thứ hai rỗng, chuỗi thứ nhất không rỗng.
4. Cả hai chuỗi có cùng độ dài và giống hệt nhau.
5. Hai chuỗi hoàn toàn không có ký tự chung.
6. Một chuỗi là Substring của chuỗi kia.
7. Một chuỗi là Subsequence của chuỗi kia.
8. Các chuỗi chỉ chứa một loại ký tự duy nhất (VD: `"aaaa"`, `"aaa"`).
9. Chuỗi chứa các ký tự đặc biệt hoặc số.
10. Bài toán yêu cầu phân biệt chữ hoa, chữ thường (Case-sensitive).
11. Không phân biệt chữ hoa, chữ thường (Case-insensitive).
12. Ký tự wildcard `*` ở đầu chuỗi (Regex, Wildcard matching).
13. Nhiều ký tự wildcard `*` liên tiếp.
14. Chuỗi có chiều dài tối đa (VD: N = 1000).
15. Khớp chữ nhưng chuỗi rỗng (`a*` với `""`).
16. Tìm Palindrome trong chuỗi không có Palindrome độ dài > 1.
17. Toàn bộ chuỗi đã là một Palindrome.
18. Độ dài chuỗi 1 (Single character string).
19. Chuỗi có số ký tự lẻ vs số ký tự chẵn (Palindromes).
20. Khoảng trắng ở đầu, cuối hoặc giữa chuỗi.
21. Pattern chỉ chứa các ký tự bắt buộc không có `*`.
22. Edit distance với Insert/Delete cost không đối xứng (Asymmetric costs).
23. Giao của nhiều Subsequence chung (LCS of 3 strings).
24. Pattern chứa `. *` khớp với toàn bộ string (Regex).
25. Mảng truy hồi cần tham chiếu giá trị âm của chỉ số.
26. Ký tự nằm ngoài bảng mã ASCII cơ bản (Unicode/UTF-8).
27. Đảo ngược thứ tự chuỗi cho kết quả khác không.
28. Pattern có `?` khớp với đúng 1 ký tự bất kỳ.
29. Thao tác Delete Operation dẫn đến xâu rỗng.
30. Tìm Subsequence trong đó có các ký tự giống nhau lặp lại nhiều lần.

## 18. 20 Common Bugs (Lỗi phổ biến)
1. Truy cập mảng vượt quá chỉ số (Index Out of Bounds `charAt(i)`).
2. Nhầm lẫn giữa `i`, `j` (1-indexed trong `dp`) và `i-1`, `j-1` (0-indexed trong `String`).
3. Quên khởi tạo Base cases (`i=0` hoặc `j=0`).
4. Viết sai công thức khi `s1.charAt(i-1) != s2.charAt(j-1)`.
5. Bị ghi đè dữ liệu quá sớm khi dùng DP mảng 1D.
6. Không lưu biến `prev` (đại diện cho `dp[i-1][j-1]`) khi tối ưu mảng 1D.
7. Lỗi Stack Overflow nếu dùng Recursion + Memoization do độ sâu đệ quy quá lớn.
8. Quên không kiểm tra chuỗi Null hoặc độ dài 0.
9. Trong bài toán Regex, xử lý sai `*` (khớp 0 ký tự hoặc nhiều ký tự).
10. Bỏ sót dấu ngoặc vòng for.
11. Đặt tên biến dễ nhầm (`i`, `j`, `m`, `n`).
12. Tính toán sai giá trị khi sử dụng hàm max() lồng nhau.
13. Xử lý DP từ cuối chuỗi nhưng duyệt nhầm từ đầu.
14. Khởi tạo `dp` bằng 0 thay vì Integer.MAX_VALUE trong các bài toán tìm min.
15. Quên điều kiện cản khi cost cộng dồn vượt quá Integer.MAX_VALUE (gây tràn số).
16. Trong Longest Palindromic Substring, đếm nhầm cả độ dài lẻ và chẵn.
17. Trong Substring, quên reset giá trị dp về 0 khi không khớp.
18. So sánh bằng `==` thay vì `equals()` nếu dùng đối tượng String thay vì char.
19. Gán nhầm kết quả cho một vị trí chưa được tính toán xong.
20. Bỏ sót điều kiện khi giải phóng bộ nhớ hoặc tái sử dụng mảng.

## 19. Giải pháp cho các lỗi (Solutions to Common Bugs)
- **Lỗi Index:** Luôn vẽ bảng 2D nhỏ trên giấy, đảm bảo mapping đúng `dp[i][j]` tương ứng char thứ `i-1`, `j-1`.
- **Tối ưu mảng 1D:** Luyện tập pattern update mảng 1 chiều thường xuyên, dùng thêm biến tạm (temporary variable).
- **Khởi tạo mảng:** Trước khi duyệt for, kiểm tra kĩ Base case. Nếu bài toán tìm Max thì khởi tạo bằng 0 hoặc -1. Tìm Min thì khởi tạo bằng vô cực.

## 20. 20 Interview Questions (Câu hỏi phỏng vấn)
1. Phân biệt Substring và Subsequence.
2. Nêu công thức LCS và độ phức tạp của nó.
3. Cách chuyển bài toán Longest Palindromic Subsequence thành LCS?
4. Khái niệm Edit Distance (Levenshtein Distance) là gì?
5. Làm sao để tối ưu không gian mảng LCS từ O(MN) xuống O(M)?
6. Trong thuật toán Edit Distance, thao tác nào ứng với `dp[i-1][j]`, `dp[i][j-1]`, và `dp[i-1][j-1]`?
7. Giải thích bài toán Interleaving String bằng DP.
8. Có thể dùng Sliding Window giải LCS không? Tại sao?
9. Thuật toán Manacher xử lý Palindrome khác DP ở điểm nào?
10. Tại sao Regular Expression Matching khó hơn Wildcard Matching?
11. Giải quyết bài toán Maximum Length of Repeated Subarray như thế nào?
12. Distinct Subsequences giải bằng DP như thế nào?
13. Nếu có 3 chuỗi, làm sao tìm LCS của cả 3?
14. Ứng dụng của thuật toán Edit Distance trong thực tiễn.
15. Cách xử lý Wildcard `*` khớp một hoặc nhiều ký tự trong DP.
16. Delete Operation for Two Strings liên quan gì đến LCS?
17. Phân tích độ phức tạp thời gian khi có `k` chuỗi cần tìm LCS.
18. Memoization vs Tabulation cho bài toán trên chuỗi, phương pháp nào tốt hơn?
19. Viết công thức DP cho chuỗi có chu trình (Circular strings).
20. Mở rộng Edit distance với các loại cost khác nhau.

## 21. So sánh với các kỹ thuật khác (Comparison with other techniques)
- **DP vs Backtracking:** Backtracking có thể sinh mọi subsequence O(2^N) - quá chậm. DP tối ưu hóa việc tính toán lặp lại, đưa về O(N^2).
- **DP vs Sliding Window:** Sliding window áp dụng tốt cho substring/subarray liên tiếp, nhưng thất bại với Subsequence.
- **DP vs Manacher:** Manacher tìm Longest Palindromic Substring tối ưu trong O(N), DP mất O(N^2) nhưng dễ viết và mở rộng.

## 22. Mẹo và Kinh nghiệm (Tips and Tricks)
- Nhớ quy tắc `1-indexed`: Để tránh kiểm tra bounds `i > 0`, `j > 0` trong vòng lặp, luôn khai báo mảng size `[m+1][n+1]`. Ký tự `s1.charAt(i-1)` tương đương hàng/cột `i`.
- Luôn in ma trận `dp` ra console (khi test nhỏ) để debug logic.

## 23. Hệ thống Bài tập thực hành (Practice Problem System)
- Chuyển sang file Problems để luyện tập 30 bài từ cơ bản đến nâng cao.

## 24. Tài liệu tham khảo (References)
- CLRS: Introduction to Algorithms.
- LeetCode DP section.
- CP-Algorithms.
