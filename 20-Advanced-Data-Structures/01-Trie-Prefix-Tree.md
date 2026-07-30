# Trie (Prefix Tree) - Cấu trúc dữ liệu và Thuật toán

## 1. Giới thiệu (Introduction)
Trie (phát âm là "try" hoặc "tree"), hay còn gọi là Prefix Tree, là một cấu trúc dữ liệu dạng cây (tree) được sử dụng để lưu trữ một mảng liên kết (associative array) nơi các key thường là các chuỗi (strings). Khác với cây tìm kiếm nhị phân (Binary Search Tree), các node trên Trie không lưu trữ key mà chúng liên kết với. Thay vào đó, vị trí của node trên cây định nghĩa key mà nó đại diện. 

## 2. Cấu trúc dữ liệu (Data Structure)
Mỗi node trong Trie bao gồm:
- Một tập hợp các liên kết đến các node con (children), mỗi liên kết tương ứng với một ký tự.
- Một cờ (boolean flag) `isEndOfWord` (hoặc `isTerminal`) để đánh dấu sự kết thúc của một từ tại node đó.

## 3. Hoạt động cơ bản (Basic Operations)
- **Insert**: Bắt đầu từ root, với mỗi ký tự trong từ, kiểm tra xem có liên kết nào tương ứng với ký tự đó không. Nếu có, di chuyển xuống node con. Nếu không, tạo một node mới và thiết lập liên kết. Cuối cùng, đánh dấu node cuối cùng là `isEndOfWord`.
- **Search**: Duyệt qua Trie theo từng ký tự của từ. Nếu bất kỳ ký tự nào không có liên kết tương ứng hoặc duyệt hết từ mà node cuối cùng không có `isEndOfWord`, từ đó không tồn tại.
- **StartsWith**: Tương tự như Search nhưng không yêu cầu node cuối cùng phải là `isEndOfWord`. Chỉ cần duyệt thành công tất cả các ký tự của tiền tố.

## 4. Phân tích độ phức tạp (Complexity Analysis)
- **Time Complexity**:
  - Insert: $O(m)$ với $m$ là độ dài của từ (word length).
  - Search: $O(m)$
  - StartsWith: $O(m)$
- **Space Complexity**: $O(N \times m)$ trong đó $N$ là số lượng từ và $m$ là độ dài trung bình của các từ, tùy thuộc vào số lượng node cần tạo.

## 5. Array vs HashMap cho Children (Array vs HashMap for Children)
- **Array**: Dùng một mảng kích thước cố định (ví dụ mảng 26 phần tử cho 26 chữ cái tiếng Anh in thường).
  - *Ưu điểm*: Truy cập nhanh với độ phức tạp $O(1)$.
  - *Nhược điểm*: Tiêu tốn nhiều bộ nhớ (Space), đặc biệt nếu Trie thưa thớt (sparse), vì mỗi node phải phân bổ mảng 26 phần tử.
- **HashMap**: Lưu trữ liên kết tới các node con bằng một Map (ví dụ `HashMap<Character, TrieNode>`).
  - *Ưu điểm*: Tiết kiệm không gian bộ nhớ (Space-efficient) cho các Trie có bảng chữ cái (alphabet) lớn hoặc rất thưa thớt.
  - *Nhược điểm*: Truy cập chậm hơn (có overhead của hash function) và sử dụng thêm bộ nhớ phụ trợ cho bản thân HashMap.

## 6. Các biến thể (Variations)
- **Compressed Trie (Radix Tree/Patricia Trie)**: Nén các node có duy nhất một node con để tiết kiệm không gian.
- **Suffix Trie**: Một Trie chứa tất cả các hậu tố (suffixes) của một từ.
- **Ternary Search Trie (TST)**: Mỗi node chứa một ký tự và ba con (nhỏ hơn, bằng, lớn hơn). Tiết kiệm không gian hơn Trie mảng, truy xuất nhanh hơn HashMap.

## 7. Ứng dụng thực tế (Real-world Applications)
- **Autocomplete/Typeahead**: Tự động hoàn thành từ khóa trên thanh tìm kiếm.
- **Spell Checker**: Kiểm tra lỗi chính tả dựa trên danh sách từ điển.
- **IP Routing (Longest Prefix Match)**: Tìm kiếm đường truyền dài nhất khớp (longest prefix match).
- **T9 Predictive Text**: Đoán từ trên bàn phím số điện thoại cũ.
- **Boggle/Word Search**: Tìm kiếm từ trong ma trận ký tự.

## 8. Khi nào nên sử dụng (When to Use)
- Tìm kiếm prefix (tiền tố) hoặc các từ trong một danh sách (dictionary) từ.
- Cần thời gian truy xuất là độ dài của từ khóa bất kể kích thước cơ sở dữ liệu lớn bao nhiêu.
- Tìm kiếm từ khớp một phần (wildcard matching).
- Sắp xếp thứ tự từ điển (lexicographical sorting) cho các chuỗi.

## 9. Khi nào không nên sử dụng (When Not to Use)
- Bảng chữ cái có kích thước rất lớn và Trie thưa thớt (gây lãng phí bộ nhớ khổng lồ).
- Không có yêu cầu tìm kiếm theo tiền tố (prefix search) và chỉ cần tìm kiếm chính xác (exact match). Trong trường hợp này, `HashSet/HashMap` có thể tốt hơn.

## 10. So sánh với các cấu trúc dữ liệu khác (Comparison)
- **Trie vs HashSet/HashMap**:
  - Hash table có thời gian lookup $O(1)$ (nếu tính trung bình, nhưng tính chi tiết là $O(m)$ với $m$ độ dài từ để tính mã hash). Trie mất $O(m)$ trong mọi trường hợp.
  - Trie không bị đụng độ băm (hash collisions).
  - Trie hỗ trợ tìm kiếm tiền tố (StartsWith), Hash Table không.
  - Trie thường tốn nhiều bộ nhớ hơn Hash table.
- **Trie vs Binary Search Tree**: BST cần $O(m \log N)$ để tìm chuỗi, chậm hơn Trie. BST không tối ưu cho chuỗi.

## 11. Triển khai mẫu (Sample Implementation in Java)

```java
class TrieNode {
    TrieNode[] children;
    boolean isEndOfWord;
    
    public TrieNode() {
        children = new TrieNode[26];
        isEndOfWord = false;
    }
}

class Trie {
    private TrieNode root;

    public Trie() {
        root = new TrieNode();
    }
    
    public void insert(String word) {
        TrieNode node = root;
        for (char c : word.toCharArray()) {
            int index = c - 'a';
            if (node.children[index] == null) {
                node.children[index] = new TrieNode();
            }
            node = node.children[index];
        }
        node.isEndOfWord = true;
    }
    
    public boolean search(String word) {
        TrieNode node = searchPrefix(word);
        return node != null && node.isEndOfWord;
    }
    
    public boolean startsWith(String prefix) {
        TrieNode node = searchPrefix(prefix);
        return node != null;
    }
    
    private TrieNode searchPrefix(String prefix) {
        TrieNode node = root;
        for (char c : prefix.toCharArray()) {
            int index = c - 'a';
            if (node.children[index] == null) {
                return null;
            }
            node = node.children[index];
        }
        return node;
    }
}
```

## 12. 20 Câu hỏi phỏng vấn phổ biến (20 Common Interview Questions)
1. Hãy triển khai Trie (Insert, Search, StartsWith).
2. Sự khác biệt giữa Trie và HashSet? Khi nào dùng cái nào?
3. Thiết kế hệ thống Autocomplete cho máy tìm kiếm.
4. Triển khai Add and Search Words Data Structure (hỗ trợ ký tự `.`).
5. Giải quyết bài toán Word Search II trên lưới 2D (2D grid).
6. Replace Words bằng chuỗi root nhỏ nhất có sẵn.
7. Tìm từ dài nhất trong một dictionary (Longest Word in Dictionary).
8. Cấu trúc dữ liệu nào tốt nhất để tìm kiếm IP routing (longest prefix match)?
9. Giải thích Radix Tree/Patricia Trie là gì? Tại sao nó tiết kiệm bộ nhớ?
10. Tìm chuỗi có XOR lớn nhất với các số trong một mảng.
11. Đếm số lượng từ có chung một tiền tố.
12. Palindrome Pairs sử dụng Trie như thế nào?
13. Prefix and Suffix Search (Tìm một từ khớp cả tiền tố và hậu tố).
14. Map Sum Pairs - tính tổng các giá trị liên kết với tiền tố.
15. Regex matching đơn giản kết hợp với Trie.
16. Tìm kiếm danh sách contact theo tên hoặc số.
17. Triển khai Boggle game solver sử dụng Trie.
18. Stream of Characters - theo dõi một luồng ký tự và kiểm tra các từ.
19. Trích xuất tất cả các từ trong Trie và trả về theo thứ tự từ điển (Lexicographical order).
20. Compressed Trie (Radix Tree) hoạt động như thế nào?

## 13. 20 Lỗi phổ biến (20 Common Bugs)
1. Quên cập nhật cờ `isEndOfWord = true` khi chèn (Insert).
2. Mảng `children` bị index out of bounds khi chuỗi chứa chữ in hoa hoặc ký tự đặc biệt.
3. Không xử lý `null` hoặc chuỗi rỗng (`""`) đầu vào.
4. Trả về `true` trong hàm Search ngay khi tìm được tiền tố, dù chưa kết thúc từ.
5. Biến con trỏ `node` không cập nhật (ví dụ quên `node = node.children[index]`).
6. Dùng nhầm chỉ mục `c - 'A'` thay vì `c - 'a'` hoặc ngược lại.
7. Xóa (Delete) từ trong Trie nhưng làm hỏng các từ khác cùng tiền tố.
8. Tràn bộ nhớ (Memory limit exceeded) vì kích thước bảng chữ cái quá lớn.
9. Quên khôi phục giá trị `isEndOfWord` khi cần quay lui (backtracking).
10. Tham chiếu sai tới biến `root` toàn cục.
11. Hàm `searchPrefix` có thể quăng exception `NullPointerException`.
12. Khi tìm tiền tố trống `""`, không trả về `root`.
13. `TrieNode` thiết kế bằng HashMap bị NullPointerException khi `get(char)` trả về `null`.
14. Khởi tạo mảng `children` với kích thước sai (ví dụ 25 thay vì 26).
15. Logic DFS khi duyệt Trie không lưu trữ (backtrack) chuỗi xây dựng đúng cách (StringBuilder deleteCharAt).
16. Nhầm lẫn giữa Node kết thúc và Node lá (leaf node). Một node có `isEndOfWord = true` có thể không phải là leaf node.
17. Kiểm tra DFS/BFS kết thúc quá sớm.
18. Không chia sẻ đúng `root` node trong class.
19. Không dọn dẹp các node thừa (dead nodes) khi xóa từ trong Trie dẫn tới memory leak giả.
20. Quên so sánh kiểu nguyên thủy đối với char/int trong thuật toán Trie bitwise.

## 14. 30 Trường hợp kiểm thử biên (30 Edge Cases)
1. Chuỗi đầu vào rỗng `""`.
2. Truy vấn tìm kiếm một chuỗi dài hơn bất kỳ chuỗi nào trong Trie.
3. Chèn một từ đã có trong Trie.
4. Chèn một từ là tiền tố của từ khác (ví dụ: `app` và `apple`).
5. Chèn từ khác là tiền tố của từ hiện tại (`apple` rồi chèn `app`).
6. Trie không có từ nào, gọi hàm Search hoặc StartsWith.
7. Search chuỗi mà có trong Trie là tiền tố nhưng không phải là một từ đầy đủ.
8. Chèn một chuỗi cực kỳ dài.
9. Kiểm tra bảng chữ cái không chỉ chứa kí tự a-z (nếu dùng mảng cố định).
10. Tìm kiếm chuỗi có ký tự hoa/thường lẫn lộn.
11. Bảng chữ cái có chứa ký tự unicode.
12. Chèn 100,000 từ giống hệt nhau (xử lý trùng lặp).
13. Chèn các từ khác biệt 1 ký tự cuối (`cat`, `car`, `cab`).
14. Tìm từ bắt đầu bằng tiền tố không tồn tại ngay từ kí tự đầu.
15. Xóa từ duy nhất trong Trie.
16. Xóa từ không tồn tại.
17. Xóa từ là tiền tố của từ khác (Xóa `app`, giữ `apple`).
18. Xóa từ mà từ khác là tiền tố (Xóa `apple`, giữ `app`).
19. StartsWith truy vấn với một từ hoàn chỉnh.
20. Chèn toàn các từ ghép chung gốc với độ sâu rất lớn (ví dụ: a, aa, aaa, aaaa...).
21. Kích thước từ điển (dictionary) lớn với các chữ cái ngẫu nhiên.
22. Khởi tạo nhiều Trie khác nhau trong cùng một hệ thống.
23. Sử dụng ký tự null byte `\0`.
24. Sử dụng dấu gạch ngang (hyphen) và khoảng trắng trong chuỗi đầu vào.
25. Mảng `children` dùng Unicode character có vượt quá 256.
26. Search tiền tố rỗng `""` nên trả về `true` (nếu không rỗng).
27. Đếm số lượng từ bắt đầu bằng tiền tố không có trong Trie (trả về 0).
28. Khớp wildcard (`.`) - khi tất cả các ký tự tiếp theo không có con.
29. Khớp wildcard ở ký tự cuối cùng.
30. Dùng các ký tự không được cho phép (nếu code tự crash khi array out of bounds).

## 15. Các lỗi bộ nhớ thường gặp (Memory Issues)
- **Object Overhead**: Ở Java, mỗi node `TrieNode` tốn bộ nhớ cho object header (khoảng 16 bytes), reference mảng (8 bytes), mảng 26 references (26 * 4/8 bytes) và boolean (1 byte, nhưng align lên).
- **Tràn RAM (Out of Memory)**: Số lượng từ quá lớn, cấu trúc dạng array 26 ở mọi node (kể cả lá) gây lãng phí.
- **Garbage Collection Overhead**: Quá nhiều đối tượng `TrieNode` sinh ra và loại bỏ dẫn tới Stop-The-World pause dài.

## 16. Tối ưu hóa (Optimizations)
- **Dùng HashMap hoặc TreeMap** cho những node thưa thớt (sparse nodes).
- **Dùng hai cấu trúc song song**: Mảng ở đỉnh cây (vì mật độ node cao) và HashMap ở đuôi cây.
- **Radix Tree (Patricia Tree)**: Nhóm các ký tự liên tiếp mà không phân nhánh thành một node duy nhất (chứa 1 `String` thay vì `char`).
- **Array-based Trie (Double-Array Trie)**: Tối ưu bộ nhớ bằng cách dùng hai mảng 1 chiều (`base` và `check`) thay vì cấu trúc con trỏ.
- **Bitwise Trie**: Đối với số nguyên (Integer) thay vì string, dùng Trie nhị phân (mỗi node chỉ có 2 con `0` và `1`) để duyệt bit.

## 17. Nền tảng toán học (Mathematical Background)
Số lượng node cực đại (worst case) của Trie là $\sum_{i=1}^{k} |W_i|$ với $|W_i|$ là độ dài chuỗi $W_i$. 
Nếu bảng chữ cái có kích thước $|\Sigma|$, bậc của mỗi node (branching factor) tối đa là $|\Sigma|$. 
Độ cao của Trie đúng bằng độ dài chuỗi dài nhất trong tập hợp.

## 18. Mẹo giải quyết bài tập (Problem Solving Tips)
- Bất cứ bài toán nào liên quan đến tập hợp nhiều từ (dictionary) và cần tra cứu nhanh (lookup), tiền tố (prefix/suffix), hãy nghĩ đến Trie đầu tiên.
- Xâu chuỗi mảng các ký tự hoặc tìm xâu chung, Trie là bạn tốt.
- XOR bài toán về mảng số: Chuyển các số thành dạng nhị phân 32-bit và đưa vào Trie để tìm chuỗi bit khớp tốt nhất (như bài Maximum XOR).
- Ma trận ký tự + Dictionary (Word Search II): Dùng DFS kết hợp với Trie. Duyệt ma trận, dùng Trie để early-pruning (cắt tỉa sớm) nếu đường đi không tương ứng với bất kỳ tiền tố nào trong Trie.

## 19. Câu hỏi đánh giá (Self-Assessment Questions)
1. Độ phức tạp không gian (Space Complexity) của Trie khi lưu trữ mảng array `children[26]` tại mỗi node là gì?
2. Sự khác biệt giữa node chứa cờ `isEndOfWord` và node lá (không có child nào)?
3. Tại sao Compressed Trie hiệu quả hơn Trie thông thường?

## 20. Hướng dẫn gỡ lỗi (Debugging Guide)
- Hãy viết một hàm `printTrie(TrieNode node, String current)` để in ra mọi từ đang có trong Trie.
- Kiểm tra kĩ logic `c - 'a'`. Nếu bài toán cho số hoặc cả chữ hoa chữ thường, mảng 26 là sai.
- Chắc chắn hàm `insert` cập nhật `isEndOfWord = true` vào **node cuối cùng**, không phải node cha của nó.

## 21. Patterns phổ biến (Common Patterns)
- **Trie + DFS/Backtracking**: Thường thấy trong các bài Word Search.
- **Trie với biến đếm (Trie Node with count)**: Node thêm trường `int count` để đếm xem có bao nhiêu chuỗi đi qua node này (dùng để prefix matching, autocomplete frequency).
- **Suffix Trie**: Đảo ngược mọi từ `word` thành `drow` và chèn vào Trie để giải các bài toán về Hậu tố (Suffix).
- **Bitwise Trie**: Trie có 2 con `0` và `1` để xử lý các phép toán XOR.

## 22. Các thư viện tiêu chuẩn (Standard Libraries)
Các ngôn ngữ cơ bản (Java, C++, Python) không có thư viện chuẩn (built-in) trực tiếp cho Trie. Tuy nhiên, ta có thể dễ dàng mô phỏng chức năng tương tự bằng `NavigableSet` hoặc `TreeSet` (ví dụ: hàm `subSet` để tìm theo prefix, nhưng không tối ưu bằng Trie gốc). Trie thường phải tự code (implement từ đầu).

## 23. Tài liệu tham khảo (References)
- "Introduction to Algorithms" - CLRS
- Các bài tập Trie trên Leetcode.
- "Algorithms, Part II" - Princeton University (Coursera).

## 24. Tóm tắt (Summary)
Trie là cấu trúc dữ liệu mạnh mẽ, đặc biệt tối ưu cho các thao tác tiền tố trên chuỗi (prefix matching) như Autocomplete và Spell Check. Dù tiêu tốn nhiều bộ nhớ hơn so với Hash Table hoặc BST, Trie vẫn không thể thay thế khi cần tìm kiếm một mảng lớn các từ vựng hoặc chuỗi dữ liệu nhị phân (bitwise). Sự tinh chỉnh như Radix Trie hoặc HashMap children giúp giảm thiểu được đáng kể nhược điểm bộ nhớ của Trie.
