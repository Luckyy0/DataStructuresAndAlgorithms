# 04. Greedy on Strings - Thuật toán Tham lam trên Chuỗi ký tự

## 1. Giới thiệu (Introduction)
Tham lam trên chuỗi (Greedy on Strings) là một phân lớp quan trọng của thuật toán Greedy, nơi ta cố gắng xây dựng, phân tích hoặc tối ưu hoá một chuỗi ký tự bằng cách đưa ra các quyết định tốt nhất ở từng bước (ví dụ: chọn ký tự lớn nhất/nhỏ nhất, ưu tiên ký tự có tần suất cao, ghép nối chuỗi ưu tiên).

## 2. Ý tưởng chính (Main Idea)
Thuật toán chia quá trình xử lý chuỗi thành các bước tuần tự (duyệt từ trái qua phải, hoặc hai chiều). Tại mỗi bước, thuật toán chọn một giải pháp tối ưu cục bộ mà không cần duyệt qua các lựa chọn thay thế. 

## 3. Các đặc điểm nhận diện (Characteristics)
- Yêu cầu xây dựng chuỗi thỏa mãn điều kiện với chi phí nhỏ nhất hoặc giá trị lớn nhất (Lexicographically smallest/largest).
- Các bài toán kiểm tra tính hợp lệ của dấu ngoặc (Parentheses balancing).
- Bài toán có thể giải quyết bằng cách đếm tần suất ký tự (Frequency counting).

## 4. Đếm tần suất và Xây dựng chuỗi tối ưu (Frequency counting and building strings optimally)
Kỹ thuật dùng mảng băm (Hash Array) hoặc Hàng đợi ưu tiên (Priority Queue) để lưu tần suất. Mỗi bước ta sẽ ưu tiên lấy ra các ký tự có tần suất cao nhất (hoặc theo thứ tự từ điển) để đưa vào chuỗi kết quả, nhằm tránh việc các ký tự giống nhau đứng cạnh nhau.

## 5. Logic Greedy với Hai con trỏ trên chuỗi (Two-pointer greedy logic on strings)
Kết hợp Greedy với kỹ thuật Two-pointer từ hai đầu của chuỗi (hoặc hai con trỏ trên hai chuỗi khác nhau). 
- Dùng trong bài toán tạo chuỗi Palindrome.
- Dùng trong việc chọn ký tự từ hai chuỗi để tạo chuỗi ghép lớn nhất (Largest Merge).

## 6. Kiểm tra cấu trúc chuỗi và Dấu ngoặc (Validating string structures - Parentheses balancing)
Dùng Greedy để duy trì số lượng dấu ngoặc mở "cần thiết" nhỏ nhất và lớn nhất. Ta có thể bỏ qua Stack truyền thống để tiết kiệm không gian bộ nhớ bằng cách theo dõi các dải hợp lệ.

## 7. Cấu trúc dữ liệu hỗ trợ (Supporting Data Structures)
- **Array/Hash Map**: Lưu số lần xuất hiện của ký tự.
- **Priority Queue (Max Heap)**: Lấy ký tự có số lần xuất hiện cao nhất.
- **Stack**: Dùng trong quá trình xây dựng chuỗi để loại bỏ ký tự (như xóa k chữ số để số nhỏ nhất).

## 8. Sắp xếp chuỗi (String Sorting)
Khi nối mảng các chuỗi để tạo ra chuỗi tổng nhỏ nhất/lớn nhất, Greedy sử dụng phép so sánh tùy chỉnh: `A + B < B + A` thay vì chỉ so sánh `A < B`.

## 9. Cắt và phân chia chuỗi (String Partitioning)
Tìm kiếm vị trí xuất hiện cuối cùng của mỗi ký tự, sau đó dùng Greedy để mở rộng phân đoạn (segment) hiện tại đến khi bao trọn toàn bộ các lần xuất hiện của các ký tự bên trong phân đoạn đó.

## 10. Các bước tiếp cận chung (General Steps to Approach)
1. Xác định tiêu chí Tham lam (Greedy Choice).
2. Xây dựng cấu trúc đếm hoặc phân tích chuỗi.
3. Duyệt chuỗi và áp dụng tiêu chí.
4. Xử lý các điều kiện biên hoặc ký tự còn thừa.

## 11. Các dạng bài toán phổ biến (Common Problem Categories)
- Lexicographical Order (Thứ tự từ điển).
- String Reorganization (Tái cấu trúc chuỗi để không có ký tự liền kề giống nhau).
- Parentheses Matching (Ghép ngoặc tối ưu).
- Anagram & Subsequence (Tạo chuỗi con/hoán vị tối ưu).

## 12. Phân tích độ phức tạp (Complexity Analysis)
- **Time Complexity**: Thông thường là $O(N)$ hoặc $O(N \log N)$ (nếu có sắp xếp hoặc dùng Heap). Mảng tần suất bảng chữ cái tiếng Anh chỉ cần $O(26) = O(1)$.
- **Space Complexity**: Thường là $O(1)$ nếu chỉ dùng mảng kích thước 26, hoặc $O(N)$ nếu cần chuỗi tạm.

## 13. Kỹ thuật Pattern Matching bằng Greedy
Dùng con trỏ duyệt qua chuỗi nguồn, luôn cố gắng khớp các ký tự của chuỗi mẫu (Pattern) ngay khi có thể. Giúp kiểm tra Subsequence trong $O(N)$.

## 14. Sự khác biệt giữa Greedy và Dynamic Programming trên Chuỗi
- **Greedy**: Không nhìn lại quá khứ, chỉ ra quyết định ở thời điểm hiện tại dựa trên heuristic cục bộ.
- **DP**: Cần xem xét tất cả các cách ghép nối chuỗi và chọn cách tốt nhất.

## 15. Kỹ thuật đếm số dư (Balance Counting)
Cho bài toán dãy ngoặc, duy trì một hoặc hai biến đếm để biểu diễn số dư ngoặc thay vì đẩy từng phần tử vào stack. Rất hữu ích cho ngoặc có chứa ký tự đại diện `*`.

## 16. Kỹ thuật Xóa ký tự (Character Deletion)
Dùng mô hình giống như Stack (hoặc string builder), xóa ký tự ở đuôi chuỗi hiện tại nếu ký tự mới thêm vào làm chuỗi kết quả "đẹp" hơn (ví dụ bài toán Remove K Digits).

## 17. 20 Câu hỏi phỏng vấn (20 Interview Questions)
1. Làm thế nào để sắp xếp lại một chuỗi sao cho không có 2 ký tự liên tiếp giống nhau?
2. Có thể kiểm tra tính hợp lệ của chuỗi ngoặc chứa ký tự `*` trong thời gian $O(N)$ và không gian $O(1)$ không?
3. Tiêu chí so sánh trong thuật toán tạo số lớn nhất từ mảng chuỗi là gì?
4. Chia chuỗi thành nhiều phần nhất có thể sao cho mỗi ký tự chỉ thuộc một phần, làm thế nào?
5. Làm thế nào để làm cho một chuỗi không còn tính đối xứng (Break a Palindrome) với thay đổi nhỏ nhất theo thứ tự từ điển?
6. Logic Greedy cho bài toán "Minimum Add to Make Parentheses Valid" là gì?
7. Giải thích bài toán "Minimum Deletions to Make Character Frequencies Unique"?
8. Để xây dựng K chuỗi Palindrome từ một chuỗi cho trước, điều kiện cần và đủ là gì?
9. Thuật toán Largest Merge of Two Strings hoạt động ra sao nếu dùng Greedy?
10. Làm sao để tìm chuỗi con chung lớn nhất (Lexicographical) thay vì dài nhất?
11. Đánh giá sự khác biệt giữa bài toán tìm Subsequence và Substring bằng Greedy.
12. Tại sao Heap lại phổ biến trong các bài tập tái cấu trúc chuỗi?
13. Giải quyết vấn đề tìm độ dài dài nhất của Palindrome có thể tạo từ các ký tự của một chuỗi.
14. "Minimum Swaps to Make Strings Balanced", thuật toán hoạt động thế nào?
15. Cách tốt nhất để nối các chuỗi tạo thành chuỗi nhỏ nhất theo thứ tự từ điển.
16. Thuật toán tìm dãy ngoặc đúng có độ dài lớn nhất (Longest Valid Parentheses) có thể dùng Greedy được không?
17. Áp dụng Greedy để tìm chuỗi con lexicographically nhỏ nhất có độ dài K.
18. Bài toán xóa các ký tự liền kề giống nhau cho đến khi không thể xóa được nữa.
19. Giải thích cách tiếp cận hai chiều (Two-pass Greedy) trên chuỗi.
20. Tại sao trong nhiều bài toán về thứ tự từ điển, đếm từ 'a' đến 'z' lại mang tính quyết định?

## 18. 20 Lỗi thường gặp (20 Common Bugs)
1. Bỏ qua các ký tự cuối cùng còn sót lại trong Heap hoặc Stack.
2. Không xử lý đúng trường hợp không thể hoàn thành (ví dụ Reorganize String trả về rỗng).
3. Sử dụng `A < B` thay vì `A + B < B + A` trong so sánh chuỗi ghép.
4. Quên trừ đi số dư ngoặc hoặc để biến số âm mà không reset.
5. Cố gắng sử dụng Greedy khi bài toán yêu cầu quay lui (Backtracking).
6. Sử dụng String concatenation (`+`) thay vì `StringBuilder` trong Java, dẫn đến TLE.
7. Sai lầm khi phân biệt chữ hoa và chữ thường trong bảng tần suất.
8. Quên cập nhật điểm đánh dấu vị trí cuối cùng trong bài toán phân chia chuỗi (Partition Labels).
9. Giải phóng Heap quá sớm khi các phần tử cùng tần suất cần xử lý đặc biệt.
10. Đếm sai số lượng ký tự lẻ trong bài toán tạo Palindrome.
11. Bỏ sót trường hợp chuỗi đầu vào chỉ có độ dài 1.
12. Không đặt điều kiện dừng chính xác khi dùng hai con trỏ trên chuỗi.
13. Nhầm lẫn giữa tính đối xứng chẵn (aabb) và lẻ (abcba) trong Greedy.
14. Cố tình thay đổi ký tự ở giữa của chuỗi Palindrome độ dài lẻ trong bài toán Break a Palindrome (không tối ưu lexicographical).
15. Xóa ký tự sai vị trí khi giải bài toán Remove K Digits.
16. Xóa hết số 0 ở đầu (Leading Zeros) nhưng chuỗi kết quả trở thành rỗng (phải trả về "0").
17. Tính sai chỉ số (Index Out of Bounds) khi kiểm tra `charAt(i+1)`.
18. Lạm dụng $O(N^2)$ như `substring` bên trong vòng lặp thay vì dùng hai con trỏ.
19. Xử lý thiếu trường hợp chuỗi toàn ký tự `*` trong bài toán Valid Parenthesis String.
20. Cập nhật mảng băm sai sau khi ký tự đã được dùng.

## 19. 30 Trường hợp góc (30 Edge Cases)
1. Chuỗi đầu vào rỗng (Empty string).
2. Chuỗi chỉ chứa một loại ký tự ("aaaaa").
3. Chuỗi có tất cả ký tự khác biệt hoàn toàn ("abcdef").
4. Chuỗi có độ dài 1 ("a").
5. Chuỗi đã sắp xếp sẵn.
6. Chuỗi đảo ngược hoàn toàn.
7. Số lần xóa hoặc thêm bằng 0.
8. `K` bằng độ dài của chuỗi.
9. `K` lớn hơn độ dài của chuỗi (K palindrome strings).
10. Chuỗi bắt đầu bằng nhiều ký tự không hợp lệ (như ngoặc đóng `)))`).
11. Dãy toàn ký tự đại diện ("****").
12. Các đoạn chứa ký tự đại diện ở giữa ("(***)").
13. Chuỗi không thể giải quyết bằng Greedy, cần trả về rỗng.
14. Các số nguyên được coi như chuỗi có chữ số 0 ở đầu ("000123").
15. Yêu cầu xóa chính xác K ký tự nhưng chuỗi có độ dài tăng dần ("12345", K=2).
16. Yêu cầu tạo K chuỗi nhưng chỉ có 1 ký tự khác nhau duy nhất.
17. Heap/Queue chứa các phần tử có tần suất giống hệt nhau.
18. Largest Merge: Hai chuỗi có đoạn tiền tố giống hệt nhau khá dài.
19. Bài toán tách từ: Một ký tự duy nhất xuất hiện ở đầu và cuối chuỗi ("abacada").
20. Minimum Swap: Các dấu ngoặc lồng nhau xen kẽ ("[]][][").
21. Break a Palindrome: "aba" đổi thành "abb" chứ không phải "aaa" (bỏ qua phần tử giữa).
22. Chuỗi có số lượng khoảng trắng thừa.
23. Gặp các ký tự đặc biệt ngoài chữ cái (nếu bài toán không đảm bảo chỉ có lowercase letters).
24. Tần suất các ký tự cần phải giảm tới 0 để duy nhất (ví dụ "a", "b", "c" đều có tần suất 1).
25. Mảng kết quả sau khi duyệt trả về lớn hơn giới hạn Integer.
26. String matching: Chuỗi pattern có độ dài bằng chuỗi đích.
27. Đếm tần suất khi chỉ có 1 ký tự với số lượng quá bán (Majority element > `(N+1)/2`).
28. Two-pointer gặp nhau ở đúng điểm giữa.
29. Cập nhật con trỏ làm vòng lặp vô hạn.
30. Yêu cầu lấy chuỗi Lexicographical lớn nhất nhưng đầu vào đã là lớn nhất.

## 20. Phân tích tối ưu theo phần cứng (Hardware & Language specifics)
Các bài toán xử lý chuỗi bằng tham lam rất nhạy cảm với việc quản lý bộ nhớ của Strings trong Java, Python, C++. Luôn sử dụng bộ đệm (như `char[]` trong Java) để sửa đổi chuỗi.

## 21. Mã giả (Pseudocode) cho một số thuật toán
**Tái cấu trúc chuỗi**:
```text
PriorityQueue pq = (đếm tần suất)
while pq.size >= 2:
    lấy ra 2 phần tử max, nối vào chuỗi
    giảm tần suất, đưa lại vào pq nếu > 0
```

## 22. Ứng dụng thực tế (Real-world Applications)
- **Nén dữ liệu**: Huffman Coding, xây dựng cây ưu tiên.
- **Biên dịch và Phân tích cú pháp**: Phân tích ngoặc hợp lệ (Parsing abstract syntax trees).
- **Lập lịch và Tối ưu hóa**: Xếp dỡ các luồng dữ liệu hoặc packet.

## 23. Bài tập thực hành tự đánh giá (Self-assessment)
Sau khi học lý thuyết này, bạn nên có khả năng giải quyết các bài toán về cấu trúc chuỗi, sắp xếp thứ tự từ điển, và đếm tần suất bằng `Map`/`Heap`.

## 24. Kết luận (Conclusion)
Thuật toán Tham lam trên Chuỗi tập trung vào việc xử lý các ký tự cục bộ tốt nhất để tạo thành tổng thể tốt nhất. Chìa khóa là phát hiện ra tính chất (property) cho phép ta không cần phải quay lui (backtrack).
