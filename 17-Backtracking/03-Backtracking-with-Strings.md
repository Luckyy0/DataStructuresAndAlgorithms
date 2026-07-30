# 03 - Backtracking với Strings

## 1. Giới thiệu (Introduction)
Backtracking (Quay lui) là một kỹ thuật mạnh mẽ để giải quyết các bài toán về tổ hợp, hoán vị và phân hoạch chuỗi. Khác với xử lý mảng thông thường, xử lý chuỗi (String) với Backtracking thường liên quan đến việc cắt chuỗi (partitioning), sinh chuỗi có cấu trúc (structured string generation) và tổ hợp ký tự.

## 2. Khái niệm cốt lõi (Core Concepts)
- **Partitioning Strings (Phân hoạch chuỗi):** Chia một chuỗi thành các phần nhỏ hơn thỏa mãn một điều kiện nhất định (ví dụ: Palindrome Partitioning).
- **Structured String Generation (Sinh chuỗi có cấu trúc):** Tạo ra các chuỗi tuân theo một quy tắc ngữ pháp hoặc ràng buộc (ví dụ: Generate Parentheses).
- **Character Combinations (Tổ hợp ký tự):** Ánh xạ các nhóm ký tự và sinh ra tất cả các tổ hợp (ví dụ: Letter Combinations of a Phone Number).

## 3. Ứng dụng thực tế (Real-world Applications)
- **Parser & Compiler:** Sinh và kiểm tra cấu trúc cú pháp (dấu ngoặc hợp lệ).
- **Xử lý ngôn ngữ tự nhiên (NLP):** Tách từ từ một chuỗi liền mạch (Word Break).
- **Mạng máy tính:** Khôi phục địa chỉ IP từ một chuỗi số.
- **Mật mã học:** Tạo mật khẩu, bẻ khóa và phân tích các tổ hợp ký tự.

## 4. Phân tích độ phức tạp (Complexity Analysis)
- **Thời gian (Time Complexity):** Thường là $O(2^N)$ (tạo tập con, tổ hợp), $O(N!)$ (hoán vị), hoặc có dạng $O(K^N)$ (như bàn phím điện thoại có $K$ lựa chọn mỗi phím).
- **Không gian (Space Complexity):** $O(N)$ cho Call Stack của đệ quy, cộng thêm không gian để lưu trữ kết quả đầu ra. Khi làm việc với Strings, việc nối chuỗi liên tục sinh ra các đối tượng mới trong Java có thể làm tăng Space Complexity, do đó nên sử dụng `StringBuilder`.

## 5. Pattern nhận dạng (Identification Patterns)
- Đề bài yêu cầu tìm **tất cả** các cách chia chuỗi.
- Yêu cầu sinh ra các chuỗi hợp lệ (dấu ngoặc, IP).
- Bài toán biến đổi hoặc thay thế ký tự trong chuỗi gốc tạo ra nhiều nhánh.

## 6. Các bước giải quyết (Steps to Solve)
1. **Xác định State:** Vị trí hiện tại trong chuỗi (ví dụ: `index` đang xét).
2. **Xác định Choices:** Các lựa chọn tại vị trí hiện tại (cắt tại đâu, thêm ký tự nào).
3. **Xác định Base Case:** Khi `index` đạt đến cuối chuỗi (hoặc đạt độ dài mong muốn).
4. **Áp dụng Constraint:** Chỉ tiếp tục nhánh đệ quy nếu lựa chọn hợp lệ (Pruning).
5. **Backtrack:** Hoàn tác trạng thái sau khi nhánh đệ quy trả về.

## 7. Template thuật toán (Algorithm Template)
```java
public void backtrack(String s, int start, StringBuilder path, List<String> result) {
    if (start == s.length()) {
        result.add(path.toString());
        return;
    }
    for (int i = start; i < s.length(); i++) {
        String sub = s.substring(start, i + 1);
        if (!isValid(sub)) continue; // Pruning
        
        int len = path.length();
        path.append(sub).append("|"); // Do
        backtrack(s, i + 1, path, result); // Recurse
        path.setLength(len); // Undo (Backtrack)
    }
}
```

## 8. Các biến thể phổ biến (Common Variations)
- Chia chuỗi theo từ điển (Word Break).
- Sinh hoán vị chuỗi (Permutation of Strings).
- Tìm chuỗi con duy nhất dài nhất hoặc nhiều nhất (Unique Substrings).

## 9. So sánh với các kỹ thuật khác (Comparison)
- **Vs DP (Quy hoạch động):** Nếu bài toán chỉ yêu cầu đếm số lượng cách hoặc kiểm tra tính khả thi, DP tốt hơn. Nếu yêu cầu in ra **tất cả** các cách, Backtracking là bắt buộc.
- **Vs DFS (Duyệt đồ thị):** Backtracking bản chất là DFS trên cây không gian trạng thái. Tuy nhiên, Backtracking nhấn mạnh vào việc "undo" trạng thái.

## 10. Cấu trúc dữ liệu hỗ trợ (Supporting Data Structures)
- `StringBuilder` (Java) hoặc mảng ký tự (`char[]`) để tối ưu việc thao tác chuỗi.
- `HashSet` / `HashMap` để tra cứu nhanh, memoization, hoặc kiểm tra trùng lặp.
- `List<String>` để lưu trữ kết quả cuối cùng.

## 11. Tối ưu hóa không gian (Space Optimization)
- Sử dụng chung một `StringBuilder` truyền qua các hàm thay vì cộng chuỗi (`String a + String b`) tạo ra rác bộ nhớ (Garbage Collection overhead).
- Dùng chỉ số `start`, `end` thay vì truyền chuỗi con mới (substring) trong tham số đệ quy.

## 12. Tối ưu hóa thời gian (Time Optimization)
- Sử dụng mảng boolean 2D hoặc mảng 1D thay thế hàm `isValid()` nếu kiểm tra lặp đi lặp lại (như bảng Palindrome cho trước).
- Early termination: Trả về sớm nếu thấy độ dài hiện tại đã vượt quá giới hạn (như Restore IP Address, một phần IP không quá 3 chữ số).

## 13. Xử lý Base Cases (Handling Base Cases)
- `index == s.length()`: Khi đã xét hết toàn bộ chuỗi gốc.
- Các điều kiện bổ sung: Ví dụ IP có đúng 4 phần (`parts == 4`), dấu ngoặc đóng/mở đã dùng hết.

## 14. Kỹ thuật Backtracking/Pruning (Pruning Techniques)
- Bỏ qua các nhánh mà tiền tố đã sai.
- Dùng Memoization (Backtracking + Memoization) để tránh tính lại các nhánh con giống nhau nếu bài toán cho phép cắt tỉa.

## 15. Tránh tính toán lặp lại (Avoiding Redundant Calculations)
- Khi chuỗi có ký tự trùng lặp, sắp xếp chuỗi (nếu có thể) và sử dụng điều kiện `if (i > start && s.charAt(i) == s.charAt(i - 1)) continue;` để tránh sinh ra các tổ hợp giống nhau.

## 16. Lỗi thường gặp (20 Common Bugs)
1. Cộng chuỗi (`+`) trong vòng lặp gây tràn bộ nhớ (Memory Limit Exceeded).
2. Quên xóa phần tử cuối cùng khỏi `StringBuilder` (`setLength`).
3. Truyền nhầm `index + 1` thay vì `i + 1` khi phân hoạch chuỗi.
4. Kiểm tra Base Case sai điều kiện (thiếu một phần cuối).
5. Quên kiểm tra `sub.isEmpty()`.
6. Lỗi OutOfBounds khi dùng `substring`.
7. Dùng `==` thay vì `.equals()` để so sánh String trong Java.
8. Sai điều kiện tỉa nhánh (Pruning quá chặt làm mất kết quả đúng).
9. Quên xử lý trường hợp chuỗi chứa ký tự '0' ở đầu khi parse số (ví dụ IP).
10. `Integer.parseInt` ném lỗi nếu chuỗi vượt quá giới hạn Int.
11. Số lượng dấu ngoặc đóng vượt quá dấu ngoặc mở trong Generate Parentheses nhưng không tỉa sớm.
12. Quên backtrack các biến đếm toàn cục.
13. Thêm tham chiếu của object tĩnh thay vì clone (không dùng cho String nhưng dùng cho List cấu thành String).
14. Lỗi tràn số nguyên khi sinh các số lớn từ chuỗi.
15. Vòng lặp rỗng nhưng vẫn đẩy kết quả vào mảng (dẫn đến danh sách chứa chuỗi rỗng).
16. Nhầm lẫn giữa độ dài `path` hiện tại và độ dài chuỗi mục tiêu.
17. Gọi hàm Backtrack nhưng quên Return trong Base Case.
18. Logic xóa phần tử sai do chỉ số cuối thay đổi (đặc biệt khi dùng `StringBuilder.delete(start, end)`).
19. Mảng hoặc `Set` memoization chưa reset đúng cách giữa các Test case (nếu biến static).
20. Phá vỡ cấu trúc UTF-16 khi thao tác surrogate pairs (ít gặp nhưng quan trọng trong NLP).

## 17. Edge Cases (30 Edge Cases)
1. Chuỗi rỗng `""`.
2. Chuỗi chỉ có 1 ký tự.
3. Chuỗi gồm toàn ký tự giống nhau (e.g., `"aaaa"`).
4. Số "0" đứng đầu (e.g., "010").
5. Chuỗi không thể phân hoạch hợp lệ (e.g., Word Break fail).
6. Độ dài chuỗi cực lớn, yêu cầu thuật toán tối ưu hoặc báo Time Limit.
7. Chuỗi chứa các ký tự đặc biệt hoặc số xen lẫn chữ cái.
8. Độ dài chuỗi vượt quá bộ đệm của một nhánh.
9. Kết quả đầu ra bị trùng lặp nhiều do chuỗi có các phần lặp lại.
10. IP address có giá trị 256 (Invalid).
11. Chuỗi chỉ gồm số 0 (e.g., "0000" cho IP => "0.0.0.0").
12. Palindrome độ dài chẵn và lẻ.
13. Input là các ký tự in hoa và thường trộn lẫn (phân biệt chữ hoa chữ thường).
14. Tạo ngoặc đơn nhưng có nhiều loại ngoặc `()`, `{}`, `[]`.
15. Không đủ ký tự để thỏa mãn yêu cầu tối thiểu (IP có ít hơn 4 số).
16. Dư thừa ký tự (IP dài hơn 12 số).
17. Dấu khoảng trắng đầu và cuối chuỗi.
18. Chuỗi có Unicode / Emoji.
19. Bàn phím điện thoại với phím 0 hoặc 1 (không có ký tự).
20. Chỉ có ngoặc đóng trong input (nếu bài toán là xóa ngoặc không hợp lệ).
21. Cần in kết quả theo thứ tự từ điển (Lexicographical order).
22. Bài toán yêu cầu phân hoạch ít nhất `K` phần.
23. Chuỗi có độ dài tối đa giới hạn bởi độ sâu đệ quy (StackOverflowError).
24. Có cả số âm (nếu lấy chuỗi trừ hoặc cộng).
25. Mọi chuỗi con đều hợp lệ.
26. Không có nhánh nào hợp lệ ngay từ bước 1.
27. Đề bài giới hạn kết quả trả về không quá `MAX_LIMIT`.
28. Tính chất đan xen (alternating characters).
29. Cấu trúc lồng nhau sâu (như Parse Lisp).
30. Tìm chuỗi hợp lệ dài nhất thay vì toàn bộ các chuỗi.

## 18. Mẹo Debug (Debugging Tips)
- In ra State `(start_index, current_path)` ở đầu hàm đệ quy để vẽ cây trạng thái.
- Đặc biệt chú ý đến độ dài của `StringBuilder` trước và sau đệ quy để đảm bảo Backtrack được thực hiện đúng cách (`path.setLength(len)`).

## 19. Nguồn tài liệu học tập (Learning Resources)
- LeetCode Patterns: Backtracking.
- Giải thuật đệ quy và quay lui trong cuốn "Competitive Programming 3" (Steven Halim).
- Các khóa học thuật toán online (Coursera, Udemy).

## 20. Câu hỏi phỏng vấn (20 Interview Questions)
1. Palindrome Partitioning
2. Letter Combinations of a Phone Number
3. Restore IP Addresses
4. Generate Parentheses
5. Split a String Into the Max Number of Unique Substrings
6. Word Break II
7. Additive Number
8. Brace Expansion
9. Letter Case Permutation
10. Remove Invalid Parentheses
11. Construct the Lexicographically Largest Valid Sequence
12. Find Unique Binary String
13. Valid Sudoku (String manipulation in grids)
14. Permutation of a given string
15. Combinations of a string
16. Longest Word in Dictionary through Deleting
17. Scramble String
18. Regular Expression Matching (Can be solved with DP, but backtracking gives intuition)
19. Ambiguous Coordinates
20. Decoding Ways (Printing all ways)

## 21. Bài tập thực hành (Practice Problems)
Xem file `03-Backtracking-with-Strings-Problems.md` để làm bài tập và đối chiếu code mẫu.

## 22. Lời khuyên cho phỏng vấn (Interview Advice)
- Luôn trình bày cách thiết kế Cây đệ quy (Recursion Tree) và giải thích Pruning trước khi viết code.
- Kỹ năng xử lý String trong Java rất quan trọng. Nhấn mạnh việc sử dụng `StringBuilder` để ăn điểm tối ưu.

## 23. Tổng kết (Summary)
Backtracking với String đòi hỏi việc quản lý tốt các chuỗi con và cách lưu trữ tạm thời (State). Sự kết hợp giữa hiểu biết về API String của ngôn ngữ và khả năng nhận diện Pruning sẽ giúp giải bài toán triệt để.

## 24. Next Steps (Các bước tiếp theo)
- Chuyển sang chủ đề Backtracking trên Ma trận (Grids & Matrices) - như N-Queens, Sudoku.
- Học kết hợp Backtracking với Bit Manipulation để tăng tốc thuật toán tổ hợp.
