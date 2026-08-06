# Cấu Trúc Dữ Liệu Trie (Prefix Tree)

## 1. Giới thiệu về Trie
Trie, hay còn được gọi là Cây tiền tố (Prefix Tree) hoặc Cây tìm kiếm kỹ thuật số (Digital Tree), là một cấu trúc dữ liệu dạng cây đặc biệt, chuyên dụng để lưu trữ các tập hợp chuỗi (strings) một cách hiệu quả. Mỗi node trong Trie đại diện cho một ký tự, và một đường đi từ gốc (root) đến một node lá (hoặc một node được đánh dấu kết thúc) sẽ tạo thành một từ hoàn chỉnh.

## 2. Tại sao gọi là Prefix Tree?
Tên gọi "Prefix Tree" xuất phát từ đặc điểm cơ bản nhất của nó: Tất cả các nhánh bắt nguồn từ một node bất kỳ sẽ có chung một tiền tố (prefix) được hình thành bởi đường đi từ root đến node đó. Điều này giúp tối ưu hóa việc tìm kiếm các từ dựa trên tiền tố của chúng.

## 3. Ưu và nhược điểm của Trie
**Ưu điểm:**
- Tìm kiếm từ và tiền tố cực kỳ nhanh (thời gian tỷ lệ thuận với độ dài từ).
- Hỗ trợ tốt các bài toán gợi ý từ (autocomplete) và sắp xếp chuỗi (lexicographical sorting).
- Không xảy ra đụng độ (collision) như trong Hash Table.

**Nhược điểm:**
- Tốn nhiều bộ nhớ không gian (Space) nếu các chuỗi không chia sẻ nhiều tiền tố chung.
- Quá tải với bảng chữ cái lớn (ví dụ bảng mã Unicode).

## 4. Cấu trúc của một Trie Node
Mỗi Trie Node thường cần ít nhất hai thành phần:
1. Cấu trúc trỏ đến các node con (chứa các ký tự tiếp theo).
2. Biến boolean `isEndOfWord` (hoặc `isEnd`) để xác định xem node hiện tại có phải là ký tự cuối cùng của một từ hợp lệ hay không.

## 5. Phân tích cài đặt: Array of 26 chars
Sử dụng mảng cố định (fixed array) cho các node con.
- **Cấu trúc:** `TrieNode[] children = new TrieNode[26];`
- **Ưu điểm:** Tốc độ truy cập cực nhanh $O(1)$, code đơn giản.
- **Nhược điểm:** Lãng phí không gian. Nếu một node chỉ có 1 con, ta vẫn phải cấp phát bộ nhớ cho mảng 26 phần tử. Phù hợp cho tiếng Anh in thường hoặc in hoa (chỉ gồm 26 ký tự).

## 6. Phân tích cài đặt: Hash Map
Sử dụng HashMap để lưu các node con.
- **Cấu trúc:** `Map<Character, TrieNode> children = new HashMap<>();`
- **Ưu điểm:** Tiết kiệm không gian triệt để. Chỉ cấp phát bộ nhớ khi có ký tự thực sự tồn tại. Hỗ trợ tốt mọi bảng chữ cái.
- **Nhược điểm:** Có độ trễ nhất định do overhead của HashMap và quá trình băm (hashing).

## 7. Cài đặt thao tác Chèn (Insert)
Để chèn một từ vào Trie: Bắt đầu từ root, duyệt qua từng ký tự của chuỗi. Nếu node tương ứng chưa tồn tại, ta tạo node mới. Di chuyển con trỏ xuống node con, lặp lại đến hết chuỗi. Ở ký tự cuối cùng, gán `isEndOfWord = true`.

## 8. Cài đặt thao tác Tìm kiếm (Search)
Để tìm kiếm một từ: Tương tự như chèn, bắt đầu từ root và duyệt theo từng ký tự của chuỗi cần tìm. Nếu tại bất kỳ bước nào node con bằng `null`, từ đó không tồn tại, trả về `false`. Nếu duyệt hết chuỗi, kiểm tra biến `isEndOfWord` tại node cuối cùng, nếu `true` trả về `true`, ngược lại `false`.

## 9. Cài đặt thao tác Tìm tiền tố (StartsWith)
Quá trình diễn ra tương tự như `Search`, tuy nhiên, sau khi duyệt hết chuỗi tiền tố (không bị gián đoạn giữa chừng bởi node `null`), ta trả về `true` ngay lập tức mà không cần quan tâm đến biến `isEndOfWord`.

## 10. Cài đặt thao tác Xóa (Delete)
Là thao tác phức tạp nhất trong Trie. Ta duyệt đệ quy (DFS) xuống node lá của từ cần xóa. Khi quay lui (backtracking), ta có thể xóa các node con nếu:
1. Node đó không được dùng chung với từ khác (không có children nào khác).
2. Node đó không phải là kết thúc của một từ khác (`isEndOfWord == false`).

## 11. Phân tích Độ phức tạp Thời gian (Time Complexity)
- **Insert:** $O(L)$ với $L$ là độ dài của từ cần chèn.
- **Search:** $O(L)$.
- **StartsWith:** $O(P)$ với $P$ là độ dài của tiền tố.
- **Delete:** $O(L)$.
Tốc độ của Trie rất ổn định và không phụ thuộc vào số lượng từ $N$ đã có trong Trie.

## 12. Phân tích Độ phức tạp Không gian (Space Complexity)
- Giả sử Trie lưu $N$ từ, độ dài trung bình là $L$, bảng chữ cái kích thước $K$.
- Trong trường hợp xấu nhất (các từ không chung tiền tố): Space Complexity là $O(N \cdot L \cdot K)$ (với Array) hoặc $O(N \cdot L)$ (với Hash Map).

## 13. Ứng dụng thực tế: Autocomplete / Typeahead
Khi người dùng nhập một vài ký tự vào thanh tìm kiếm, Trie có thể nhanh chóng dùng `startsWith` để đi đến node kết thúc của tiền tố đó. Từ node này, ta dùng thuật toán BFS hoặc DFS để thu thập tất cả các từ có thể hoàn thiện từ tiền tố này.

## 14. Ứng dụng thực tế: Bitwise XOR Trie
Để tìm XOR lớn nhất của hai số trong mảng, ta biến đổi các số thành chuỗi nhị phân (32 bits) và lưu vào Trie. Để tối đa hóa XOR với một số $X$, ta ưu tiên duyệt vào nhánh có bit ngược lại với bit hiện tại của $X$.

## 15. Ứng dụng thực tế: Routing Table / Longest Prefix Match
Trie được dùng trong hệ thống mạng để tìm đường dẫn (IP routing) với kỹ thuật tìm tiền tố dài nhất khớp (Longest Prefix Matching) giúp gói tin đi đúng hướng.

## 16. So sánh Trie và Hash Map
- **Tìm kiếm chính xác:** Hash Map trung bình $O(1)$ (nếu bỏ qua thời gian băm chuỗi $O(L)$), Trie mất $O(L)$.
- **Tìm kiếm tiền tố:** Trie là giải pháp duy nhất (Hash Map không thể làm được).
- **Bộ nhớ:** Trie tốn nhiều bộ nhớ hơn nếu không chia sẻ nhiều tiền tố, nhưng lại tối ưu hơn Hash Map nếu có nhiều từ chung tiền tố.

## 17. So sánh Trie và Binary Search Tree
- BST so sánh toán bộ chuỗi mất $O(L)$, độ cao cây là $\log N$. Do đó tìm kiếm mất $O(L \log N)$.
- Trie mất thời gian $O(L)$ hằng số không phụ thuộc vào $N$. Do đó Trie nhanh hơn nhiều so với BST khi thao tác với String.

## 18. Cấu trúc mở rộng: Compressed Trie (Radix Tree)
Để khắc phục nhược điểm tốn bộ nhớ, Compressed Trie nhóm các node có một con duy nhất liên tiếp nhau lại thành một node chứa nhiều ký tự (một chuỗi) thay vì chỉ một ký tự.

## 19. Cấu trúc mở rộng: Suffix Trie
Bằng cách thêm tất cả các hậu tố (suffixes) của một chuỗi vào Trie, ta có Suffix Trie, hỗ trợ tìm kiếm chuỗi con (substring) rất hiệu quả (mặc dù Suffix Tree hoặc Suffix Array tối ưu hơn cho mục đích thực tế).

## 20. 20 Câu hỏi phỏng vấn phổ biến
1. Trie là gì và nó khác biệt như thế nào so với Hash Table khi lưu trữ chuỗi?
2. Tại sao cấu trúc này lại được gọi là Prefix Tree?
3. Phân tích Time Complexity và Space Complexity của thao tác Insert và Search trong Trie.
4. Đánh đổi (trade-off) giữa việc dùng mảng kích thước 26 và HashMap trong Trie Node là gì?
5. Làm thế nào để cài đặt thao tác Delete để giải phóng bộ nhớ an toàn trong Trie?
6. Trie có hỗ trợ Wildcard Search (ví dụ dấu `.` đại diện ký tự bất kỳ) không? Thực hiện như thế nào?
7. Giải thích cách xây dựng tính năng Autocomplete (gợi ý từ) sử dụng Trie.
8. Bitwise Trie là gì? Tại sao nó được dùng để giải quyết bài toán Maximum XOR?
9. Longest Prefix Matching trong IP routing liên quan gì đến Trie?
10. Làm thế nào để lưu trữ danh bạ điện thoại bằng Trie?
11. Nêu sự khác biệt giữa Prefix Trie và Suffix Trie.
12. Radix Tree (Compressed Trie) khác gì so với Trie thông thường và giải quyết vấn đề gì?
13. Thuật toán Aho-Corasick sử dụng Trie như thế nào để tìm kiếm nhiều mẫu cùng lúc?
14. Khi bảng chữ cái hỗ trợ Unicode (rất lớn), cấu trúc Trie Node nên được thiết kế như thế nào?
15. Nếu bộ dữ liệu chuỗi quá lớn không vừa trong RAM, có cấu trúc Trie nào chạy trên Disk (Disk-based Trie) không?
16. Có thể sử dụng Trie để lưu trữ mảng số nguyên không?
17. Điều kiện tồi tệ nhất về mặt không gian (Worst-case Space) của Trie so với mảng các chuỗi là gì?
18. Có cách nào lưu thêm biến đếm (`count`) tại mỗi node để đếm số từ chứa chung tiền tố không?
19. Giải bài toán "Tìm từ dài nhất trong từ điển được tạo ra bằng cách thêm từng ký tự một" bằng Trie.
20. Nếu muốn tìm palindrome substring, có áp dụng được Trie hay Suffix Trie không?

## 21. 20 Lỗi phổ biến khi code Trie
1. Quên khởi tạo mảng con hoặc HashMap trong constructor của TrieNode.
2. Quên đặt cờ `isEndOfWord = true` sau vòng lặp của hàm `insert`.
3. Hàm `search` trả về `true` chỉ vì chạy hết chuỗi mà quên check thuộc tính `isEndOfWord`.
4. Không kiểm tra giá trị null trước khi đi xuống các node con, dẫn đến `NullPointerException`.
5. Tính sai index của mảng khi code: dùng `char - 'a'` nhưng input có chứa chữ hoa `A`.
6. Lỗi `IndexOutOfBoundsException` vì chuỗi đầu vào chứa các ký tự ngoài bảng 26 chữ cái.
7. Xóa node một cách thủ công sai cách, làm mất luôn phần tiền tố mà từ khác đang sử dụng.
8. Hàm `startsWith` bị cài đặt sai khi lấy điều kiện trả về là `isEndOfWord` (điều này sai với logic tiền tố).
9. Gây ra vòng lặp vô hạn (Infinite loop) do sử dụng logic DFS sai trong các bài toán đồ thị kết hợp Trie.
10. Hàm Insert không xử lý việc một từ có thể chèn lại nhiều lần.
11. Tạo biến `static` cho `TrieNode` root, gây lỗi ở các test cases tiếp theo trên platform thi code.
12. Quên xử lý chuỗi rỗng `""`. Mặc dù ít khi đề cho `""`, nhưng nếu có sẽ gây lỗi NullPointer hoặc Exception.
13. Vô tình gán đè cờ `isEndOfWord = false` khi thêm từ có tiền tố trùng với một từ đã thêm trước đó.
14. Trong Word Search II: Không backtrack đánh dấu visited, hoặc backtracking sai trạng thái của Trie Node.
15. Không chú ý cập nhật biến đếm trung gian nếu Trie được yêu cầu dùng để đếm số lượng từ.
16. Nhầm lẫn khi gọi đệ quy trong bài thiết kế Magic Dictionary (chỉ cho phép đổi 1 ký tự).
17. Dùng HashMap nhưng gọi `.get()` không cẩn thận và bị lỗi thay vì gọi `.containsKey()` trước.
18. Trie thao tác bit (Bitwise Trie): Lỗi xử lý bit dấu (sign bit) với các số nguyên âm trong Java.
19. Cắt đứt liên kết từ cha xuống con sai cách trong phép toán Delete (cần gán reference bằng null).
20. Xây dựng Suffix Trie mất $O(N^2)$ dẫn đến Time Limit Exceeded (TLE) trong khi lẽ ra chỉ cần Prefix Trie đơn giản.

## 22. 30 Trường hợp biên (Edge Cases)
1. Chèn một chuỗi rỗng `""`.
2. Tìm kiếm (Search) một chuỗi rỗng.
3. Tìm kiếm tiền tố (StartsWith) một chuỗi rỗng.
4. Đầu vào chứa các chuỗi giống hệt nhau nhiều lần.
5. Từ dài nhất có độ dài cực lớn (tràn stack nếu dùng đệ quy duyệt).
6. Tìm kiếm một chuỗi dài hơn bất kỳ chuỗi nào trong Trie.
7. Tìm kiếm một từ là tiền tố của một từ có trong Trie, nhưng không có lá ở đó.
8. Tất cả các chuỗi trong Trie có chung một tiền tố duy nhất, nhưng lại chia rẽ ở ký tự cuối.
9. Các chuỗi trong Trie không có bất kỳ tiền tố chung nào (như các ký tự root hoàn toàn khác biệt).
10. Chèn và tìm kiếm với Trie chỉ có đúng 1 từ duy nhất.
11. Bảng chữ cái có lẫn lộn chữ thường và chữ hoa (cần mảng 52 phần tử hoặc Hash Map).
12. Chuỗi chứa ký tự số từ '0' đến '9'.
13. Ký tự nhập vào chứa khoảng trắng hoặc ký tự đặc biệt (`!`, `@`, `#`).
14. Bảng mã Unicode với các ký tự 2-byte hoặc 4-byte (ví dụ emoji).
15. Yêu cầu xóa (Delete) một từ không hề tồn tại trong Trie.
16. Yêu cầu xóa một từ, và từ đó đang là tiền tố cho một từ dài hơn trong Trie.
17. Yêu cầu xóa một từ, và từ đó chứa các từ khác đang đóng vai trò tiền tố của nó.
18. Xóa tất cả các từ trong Trie cho đến khi Trie rỗng hoàn toàn.
19. Tìm kiếm tiền tố trên một Trie chưa được chèn từ nào (Trie rỗng).
20. `startsWith` trả về true cho một tiền tố, nhưng thực chất tiền tố này đã là một từ hợp lệ.
21. Bitwise Trie gặp phải số 0.
22. Bitwise Trie gặp phải các số âm, cần xử lý toán tử dịch bit nhị phân an toàn `>>>`.
23. Bitwise Trie có `Integer.MAX_VALUE` và `Integer.MIN_VALUE`.
24. Cập nhật giá trị đếm (`sum`) trong Map Sum Pairs khi insert trùng từ (cần trừ đi val cũ trước khi cộng val mới).
25. Memory Limit Exceeded do dùng mảng 256 ký tự cho hàng triệu node, thay vì Hash Map.
26. Thuật toán Search hỗ trợ Wildcard (như `.`) nhưng wildcard lại ở vị trí ký tự đầu tiên.
27. Đếm số từ có tiền tố `P`, nhưng không có từ nào khớp (trả về 0).
28. Khởi tạo mảng String rỗng hoặc mảng null truyền vào khối khởi tạo Trie.
29. Cây Trie có dạng hình ống (LinkedList) khi tất cả các từ bổ sung là các từ dài hơn và bao hàm nhau.
30. Tìm kiếm từ có tiền tố trùng khớp nhưng chuỗi tìm kiếm dài hơn chuỗi lưu trữ.

## 23. Kinh nghiệm tối ưu hóa Trie
Luôn cân nhắc giữa mảng (Array) và HashMap. Với bảng 26 chữ cái viết thường và ràng buộc thời gian nghiêm ngặt, Array là tối ưu. Với bộ dữ liệu rộng và tốn memory, hãy đổi sang HashMap. Cố gắng sử dụng vòng lặp lặp (Iterative) cho Search và Insert để tránh quá tải bộ nhớ ngăn xếp (Stack Overflow) do đệ quy sâu.

## 24. Lời kết
Trie là một trong những cấu trúc dữ liệu thanh lịch và quyền năng nhất khi xử lý văn bản và thao tác bit. Dù có tốn một lượng không gian nhớ nhất định, nó đem lại tốc độ tra cứu vượt trội mà ít cấu trúc nào đuổi kịp, là kiến thức thiết yếu đối phó với bài toán chuỗi hóc búa.
