# Bài tập Cấu Trúc Dữ Liệu Trie (Prefix Tree)

Dưới đây là 30 bài tập quan trọng liên quan đến cấu trúc dữ liệu Trie. 10 bài đầu tiên cung cấp lời giải và phân tích chi tiết.

---

## 1. Implement Trie (Prefix Tree)

### Đề bài chi tiết
Thiết kế cấu trúc dữ liệu Trie hỗ trợ các thao tác:
- `Trie()`: Khởi tạo đối tượng Trie.
- `void insert(String word)`: Chèn chuỗi `word` vào Trie.
- `boolean search(String word)`: Trả về `true` nếu `word` tồn tại trong Trie, ngược lại trả về `false`.
- `boolean startsWith(String prefix)`: Trả về `true` nếu có ít nhất một từ trong Trie có tiền tố là `prefix`, ngược lại trả về `false`.

### Phân tích thuật toán
Tạo lớp `TrieNode` có chứa một mảng `children` gồm 26 phần tử tương ứng với 26 chữ cái (từ 'a' đến 'z') và một biến boolean `isEnd` để xác định đây có phải là ký tự cuối của một từ không. Các phép chèn và tìm kiếm sẽ duyệt bắt đầu từ root.

### Mã nguồn Java
```java
class TrieNode {
    TrieNode[] children;
    boolean isEnd;
    public TrieNode() {
        children = new TrieNode[26];
        isEnd = false;
    }
}

class Trie {
    private TrieNode root;

    public Trie() {
        root = new TrieNode();
    }
    
    public void insert(String word) {
        TrieNode curr = root;
        for (char c : word.toCharArray()) {
            int index = c - 'a';
            if (curr.children[index] == null) {
                curr.children[index] = new TrieNode();
            }
            curr = curr.children[index];
        }
        curr.isEnd = true;
    }
    
    public boolean search(String word) {
        TrieNode node = searchPrefix(word);
        return node != null && node.isEnd;
    }
    
    public boolean startsWith(String prefix) {
        return searchPrefix(prefix) != null;
    }
    
    private TrieNode searchPrefix(String prefix) {
        TrieNode curr = root;
        for (char c : prefix.toCharArray()) {
            int index = c - 'a';
            if (curr.children[index] == null) {
                return null;
            }
            curr = curr.children[index];
        }
        return curr;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity)**: $O(L)$ cho mọi thao tác (`insert`, `search`, `startsWith`), với $L$ là độ dài từ.
- **Không gian (Space Complexity)**: $O(L)$ trung bình mỗi lần `insert`, $O(1)$ cho `search`.

---

## 2. Design Add and Search Words Data Structure

### Đề bài chi tiết
Thiết kế cấu trúc dữ liệu thêm từ và tìm kiếm từ. Khi tìm kiếm, ký tự dấu chấm `.` có thể đại diện cho bất kỳ ký tự nào. Hỗ trợ 2 hàm `addWord(word)` và `search(word)`.

### Phân tích thuật toán
Sử dụng Trie thông thường để thêm từ. Tuy nhiên, hàm tìm kiếm cần hỗ trợ dấu `.`. Khi gặp dấu `.`, chúng ta phải dùng thuật toán Đệ quy hoặc Duyệt theo chiều sâu (DFS) để thử duyệt vào tất cả các node con (26 ký tự) hiện có.

### Mã nguồn Java
```java
class WordDictionary {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean isEnd = false;
    }
    
    TrieNode root;

    public WordDictionary() {
        root = new TrieNode();
    }
    
    public void addWord(String word) {
        TrieNode curr = root;
        for (char c : word.toCharArray()) {
            int idx = c - 'a';
            if (curr.children[idx] == null) {
                curr.children[idx] = new TrieNode();
            }
            curr = curr.children[idx];
        }
        curr.isEnd = true;
    }
    
    public boolean search(String word) {
        return searchDFS(word.toCharArray(), 0, root);
    }
    
    private boolean searchDFS(char[] word, int index, TrieNode node) {
        if (index == word.length) return node.isEnd;
        
        char c = word[index];
        if (c == '.') {
            for (int i = 0; i < 26; i++) {
                if (node.children[i] != null && searchDFS(word, index + 1, node.children[i])) {
                    return true;
                }
            }
            return false;
        } else {
            int idx = c - 'a';
            if (node.children[idx] == null) return false;
            return searchDFS(word, index + 1, node.children[idx]);
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: Thêm từ: $O(L)$. Tìm kiếm: Tồi tệ nhất là $O(26^L)$ khi chuỗi toàn ký tự `.`, tốt nhất là $O(L)$.
- **Không gian**: $O(L)$ cho stack gọi đệ quy trong lúc tìm kiếm.

---

## 3. Word Search II

### Đề bài chi tiết
Cho mảng hai chiều kích thước $m \times n$ chứa các ký tự và danh sách các chuỗi (words). Trả về tất cả các từ tồn tại trong lưới ký tự. Từ được hình thành bằng cách duyệt các ô lân cận (lên, xuống, trái, phải).

### Phân tích thuật toán
Tạo Trie từ mảng các chuỗi cần tìm. Duyệt mọi ô trên bảng $m \times n$. Với mỗi ô, sử dụng thuật toán Backtracking kết hợp DFS kết hợp duyệt Trie (chỉ đi tiếp nếu ký tự khớp với nhánh trong Trie). Để chống trùng lặp từ kết quả, khi tìm thấy từ, đánh dấu `node.word = null`.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        String word = null;
    }
    
    public List<String> findWords(char[][] board, String[] words) {
        List<String> result = new ArrayList<>();
        TrieNode root = new TrieNode();
        for (String w : words) {
            TrieNode curr = root;
            for (char c : w.toCharArray()) {
                if (curr.children[c - 'a'] == null) {
                    curr.children[c - 'a'] = new TrieNode();
                }
                curr = curr.children[c - 'a'];
            }
            curr.word = w;
        }
        
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                dfs(board, i, j, root, result);
            }
        }
        return result;
    }
    
    private void dfs(char[][] board, int i, int j, TrieNode node, List<String> result) {
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length || board[i][j] == '#' || node.children[board[i][j] - 'a'] == null) {
            return;
        }
        
        char c = board[i][j];
        node = node.children[c - 'a'];
        if (node.word != null) {
            result.add(node.word);
            node.word = null; // Tránh thêm lại từ đã tìm thấy
        }
        
        board[i][j] = '#'; // Mark visited
        dfs(board, i - 1, j, node, result);
        dfs(board, i + 1, j, node, result);
        dfs(board, i, j - 1, node, result);
        dfs(board, i, j + 1, node, result);
        board[i][j] = c; // Backtrack
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(M \times N \times 4^L)$ với $L$ là độ dài cực đại của chuỗi, $M, N$ là kích thước bảng.
- **Không gian**: $O(\sum K_i)$ với $K_i$ là độ dài các từ để xây dựng Trie.

---

## 4. Maximum XOR of Two Numbers in an Array

### Đề bài chi tiết
Cho một mảng các số nguyên. Tìm giá trị lớn nhất của $nums[i] \oplus nums[j]$ (XOR). Giải thuật phải chạy trong $O(N)$.

### Phân tích thuật toán
Sử dụng Bitwise Trie. Chuyển các số thành dạng nhị phân 32-bit. Đưa tất cả vào Trie. Sau đó với mỗi số $x$, ta duyệt Trie để tìm số mang lại kết quả XOR lớn nhất. Bí quyết ở chỗ: Tại mỗi bit, ưu tiên đi theo nhánh có bit đảo ngược của bit hiện tại (nếu bit là 0 thì kiếm nhánh 1, và ngược lại) để bit kết quả luôn là 1.

### Mã nguồn Java
```java
class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[2];
    }
    
    public int findMaximumXOR(int[] nums) {
        TrieNode root = new TrieNode();
        for (int num : nums) {
            TrieNode curr = root;
            for (int i = 31; i >= 0; i--) {
                int bit = (num >>> i) & 1;
                if (curr.children[bit] == null) {
                    curr.children[bit] = new TrieNode();
                }
                curr = curr.children[bit];
            }
        }
        
        int max = 0;
        for (int num : nums) {
            TrieNode curr = root;
            int currSum = 0;
            for (int i = 31; i >= 0; i--) {
                int bit = (num >>> i) & 1;
                int toggleBit = 1 - bit;
                if (curr.children[toggleBit] != null) {
                    currSum += (1 << i);
                    curr = curr.children[toggleBit];
                } else {
                    curr = curr.children[bit];
                }
            }
            max = Math.max(max, currSum);
        }
        return max;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(N)$ vì mỗi số xử lý 32 thao tác (hằng số).
- **Không gian**: $O(N)$ do tạo tối đa $32 \times N$ nodes.

---

## 5. Replace Words

### Đề bài chi tiết
Cho một danh sách các từ điển (dictionary) đóng vai trò là "gốc rễ" (roots). Và một câu gồm các từ tách nhau bằng khoảng trắng. Đề bài yêu cầu thay thế các từ trong câu bằng gốc tương ứng ngắn nhất có thể nếu nó được tìm thấy.

### Phân tích thuật toán
Xây dựng Trie từ các từ trong `dictionary`. Đối với mỗi từ trong chuỗi đầu vào, đi vào Trie để tìm tiền tố ngắn nhất là một từ hoàn chỉnh (`isEnd == true`). Nếu có thì sử dụng tiền tố đó, nếu không, giữ nguyên từ cũ.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        String word;
    }
    
    public String replaceWords(List<String> dictionary, String sentence) {
        TrieNode root = new TrieNode();
        for (String d : dictionary) {
            TrieNode curr = root;
            for (char c : d.toCharArray()) {
                if (curr.children[c - 'a'] == null) curr.children[c - 'a'] = new TrieNode();
                curr = curr.children[c - 'a'];
            }
            curr.word = d;
        }
        
        StringBuilder sb = new StringBuilder();
        for (String word : sentence.split(" ")) {
            if (sb.length() > 0) sb.append(" ");
            TrieNode curr = root;
            for (char c : word.toCharArray()) {
                if (curr.children[c - 'a'] == null || curr.word != null) {
                    break;
                }
                curr = curr.children[c - 'a'];
            }
            sb.append(curr.word != null ? curr.word : word);
        }
        return sb.toString();
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(N \cdot L + M \cdot L)$ ($N$ là từ điển, $M$ là độ dài câu).
- **Không gian**: $O(N \cdot L)$ cho bộ nhớ Trie.

---

## 6. Prefix and Suffix Search

### Đề bài chi tiết
Thiết kế `WordFilter(String[] words)` chấp nhận danh sách từ. Triển khai phương thức `f(String prefix, String suffix)` trả về index lớn nhất của từ khớp cả tiền tố và hậu tố. 

### Phân tích thuật toán
Biến đổi chuỗi chèn vào Trie bằng cấu trúc `suffix + "{" + word`. Với mỗi từ `apple`, ta chèn `apple{apple`, `pple{apple`, `ple{apple`, `le{apple`, `e{apple`, `{apple`. Đánh dấu Trie bằng chỉ số vị trí (index) để dễ dàng tìm kiếm hàm `f(prefix, suffix)` thông qua tìm truy vấn chuỗi `suffix + "{" + prefix`. 

### Mã nguồn Java
```java
class WordFilter {
    class TrieNode {
        TrieNode[] children = new TrieNode[27]; // 26 chars + '{'
        int weight = 0;
    }
    
    TrieNode root;

    public WordFilter(String[] words) {
        root = new TrieNode();
        for (int weight = 0; weight < words.length; weight++) {
            String word = words[weight] + "{";
            for (int i = 0; i < word.length(); i++) {
                TrieNode curr = root;
                curr.weight = weight;
                for (int j = i; j < 2 * word.length() - 1; j++) {
                    int k = word.charAt(j % word.length()) - 'a';
                    if (k < 0) k = 26; // character '{'
                    if (curr.children[k] == null) curr.children[k] = new TrieNode();
                    curr = curr.children[k];
                    curr.weight = weight;
                }
            }
        }
    }
    
    public int f(String pref, String suff) {
        TrieNode curr = root;
        String searchWord = suff + "{" + pref;
        for (char c : searchWord.toCharArray()) {
            int k = c == '{' ? 26 : c - 'a';
            if (curr.children[k] == null) return -1;
            curr = curr.children[k];
        }
        return curr.weight;
    }
}
```

### Độ phức tạp
- **Thời gian**: Khởi tạo $O(N \times L^2)$. Truy vấn $O(P + S)$ độ dài prefix, suffix.
- **Không gian**: $O(N \times L^2)$.

---

## 7. Implement Magic Dictionary

### Đề bài chi tiết
Thiết kế cấu trúc từ điển `MagicDictionary` có thể chèn từ. Cần cung cấp hàm `search` cho biết từ đó có khớp với đúng **một lỗi ký tự** duy nhất (edit distance = 1, thay thế 1 chữ) so với từ nào trong bộ lưu trữ không.

### Phân tích thuật toán
Tạo Trie chuẩn. Trong hàm `search`, ta duyệt đệ quy (DFS). Ta được quyền "đi chệch hướng" chính xác 1 lần qua các nhánh khác nếu đã tìm thấy sai sót.

### Mã nguồn Java
```java
class MagicDictionary {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean isEnd = false;
    }
    
    TrieNode root;

    public MagicDictionary() {
        root = new TrieNode();
    }
    
    public void buildDict(String[] dictionary) {
        for (String word : dictionary) {
            TrieNode curr = root;
            for (char c : word.toCharArray()) {
                if (curr.children[c - 'a'] == null) curr.children[c - 'a'] = new TrieNode();
                curr = curr.children[c - 'a'];
            }
            curr.isEnd = true;
        }
    }
    
    public boolean search(String searchWord) {
        return dfs(root, searchWord.toCharArray(), 0, false);
    }
    
    private boolean dfs(TrieNode node, char[] word, int idx, boolean modified) {
        if (idx == word.length) {
            return node.isEnd && modified;
        }
        char c = word[idx];
        for (int i = 0; i < 26; i++) {
            if (node.children[i] != null) {
                if (i == c - 'a') {
                    if (dfs(node.children[i], word, idx + 1, modified)) return true;
                } else if (!modified) {
                    if (dfs(node.children[i], word, idx + 1, true)) return true;
                }
            }
        }
        return false;
    }
}
```

### Độ phức tạp
- **Thời gian**: Khởi tạo $O(N \cdot L)$. Tìm kiếm $O(26 \cdot L)$.
- **Không gian**: $O(N \cdot L)$.

---

## 8. Map Sum Pairs

### Đề bài chi tiết
Thiết kế `MapSum` lưu một cặp (key, val). Trả về tổng các `val` của tất cả các `key` có cùng một `prefix`.

### Phân tích thuật toán
Tạo Trie. Ở mỗi `TrieNode` lưu trữ giá trị `sum` cộng dồn. Dùng một `HashMap` bổ sung để check xem key đã tồn tại trước đó hay chưa, để có thể khấu trừ (delta = newVal - oldVal) nếu gặp lệnh đè.

### Mã nguồn Java
```java
import java.util.*;

class MapSum {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        int sum = 0;
    }
    
    TrieNode root;
    Map<String, Integer> map;

    public MapSum() {
        root = new TrieNode();
        map = new HashMap<>();
    }
    
    public void insert(String key, int val) {
        int delta = val - map.getOrDefault(key, 0);
        map.put(key, val);
        TrieNode curr = root;
        for (char c : key.toCharArray()) {
            if (curr.children[c - 'a'] == null) {
                curr.children[c - 'a'] = new TrieNode();
            }
            curr = curr.children[c - 'a'];
            curr.sum += delta;
        }
    }
    
    public int sum(String prefix) {
        TrieNode curr = root;
        for (char c : prefix.toCharArray()) {
            if (curr.children[c - 'a'] == null) return 0;
            curr = curr.children[c - 'a'];
        }
        return curr.sum;
    }
}
```

### Độ phức tạp
- **Thời gian**: Thêm / Tính $O(L)$.
- **Không gian**: $O(N \cdot L)$ và HashMap $O(N)$.

---

## 9. Longest Word in Dictionary

### Đề bài chi tiết
Tìm từ dài nhất trong một danh sách có thể xây dựng lần lượt bằng các từ ngắn hơn trong mảng. Nếu có nhiều từ cùng chiều dài, chọn từ nhỏ hơn theo thứ tự từ điển.

### Phân tích thuật toán
Thêm toàn bộ từ vào Trie. Sau đó duyệt DFS từ root. Ở mỗi nhánh con, chỉ tiếp tục đi xuống nếu `isEnd == true` (nghĩa là tiền tố tới điểm đó bản thân nó cũng là một từ hợp lệ). Tìm độ dài lớn nhất.

### Mã nguồn Java
```java
class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        String word;
    }
    
    TrieNode root = new TrieNode();
    String res = "";
    
    public String longestWord(String[] words) {
        for (String word : words) {
            TrieNode curr = root;
            for (char c : word.toCharArray()) {
                if (curr.children[c - 'a'] == null) curr.children[c - 'a'] = new TrieNode();
                curr = curr.children[c - 'a'];
            }
            curr.word = word;
        }
        
        dfs(root);
        return res;
    }
    
    private void dfs(TrieNode node) {
        if (node.word != null) {
            if (node.word.length() > res.length() || (node.word.length() == res.length() && node.word.compareTo(res) < 0)) {
                res = node.word;
            }
        }
        
        for (int i = 0; i < 26; i++) {
            if (node.children[i] != null && node.children[i].word != null) {
                dfs(node.children[i]);
            }
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: Khởi tạo $O(\Sigma L)$. Duyệt $O(\Sigma L)$.
- **Không gian**: $O(\Sigma L)$.

---

## 10. Stream of Characters

### Đề bài chi tiết
Khởi tạo cấu trúc `StreamChecker` bằng một mảng từ. Nó xử lý stream các ký tự lần lượt bằng cách gọi hàm `query(letter)`. Hàm này trả về `true` nếu trong dòng các ký tự vừa query tính tới thời điểm hiện tại, có tồn tại mảng con tận cùng khớp với một từ trong mảng gốc.

### Phân tích thuật toán
Xây dựng một Trie lưu các từ bị **đảo ngược**. Sử dụng một `StringBuilder` lưu lại luồng chữ cái do `query` gọi liên tiếp. Truy vấn ngược về phía đầu chuỗi.

### Mã nguồn Java
```java
class StreamChecker {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean isEnd;
    }
    
    TrieNode root = new TrieNode();
    StringBuilder stream = new StringBuilder();

    public StreamChecker(String[] words) {
        for (String word : words) {
            TrieNode curr = root;
            for (int i = word.length() - 1; i >= 0; i--) {
                char c = word.charAt(i);
                if (curr.children[c - 'a'] == null) curr.children[c - 'a'] = new TrieNode();
                curr = curr.children[c - 'a'];
            }
            curr.isEnd = true;
        }
    }
    
    public boolean query(char letter) {
        stream.append(letter);
        TrieNode curr = root;
        for (int i = stream.length() - 1; i >= 0; i--) {
            char c = stream.charAt(i);
            if (curr.children[c - 'a'] == null) return false;
            curr = curr.children[c - 'a'];
            if (curr.isEnd) return true;
        }
        return false;
    }
}
```

### Độ phức tạp
- **Thời gian**: Truy vấn tối đa độ dài của từ $O(\max(L))$, thường $L \le 200$. Khởi tạo $O(\Sigma L)$.
- **Không gian**: Khởi tạo $O(\Sigma L)$.

---

## 11. Palindrome Pairs (LeetCode 336)

### Đề bài chi tiết
Cho một danh sách các từ độc nhất, hãy tìm tất cả các cặp chỉ số `(i, j)` sao cho việc ghép hai từ `words[i] + words[j]` tạo thành một chuỗi Palindrome (chuỗi đối xứng).

### Phân tích thuật toán
Lưu các chuỗi bị đảo ngược vào Trie, lưu trữ `index` của chuỗi ở `isEnd` và một danh sách `palindromePrefixIndices` chứa các index của những từ có phần hậu tố (tiền tố khi đã đảo ngược) là chuỗi đối xứng. Khi duyệt một chuỗi, ta xét từng ký tự một, kiểm tra xem phần còn lại có là đối xứng hay không.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        int wordIndex = -1;
        List<Integer> palindromePrefixIndices = new ArrayList<>();
    }

    private boolean isPalindrome(String s, int left, int right) {
        while (left < right) {
            if (s.charAt(left++) != s.charAt(right--)) return false;
        }
        return true;
    }

    public List<List<Integer>> palindromePairs(String[] words) {
        TrieNode root = new TrieNode();
        for (int i = 0; i < words.length; i++) {
            String word = words[i];
            String reversedWord = new StringBuilder(word).reverse().toString();
            TrieNode curr = root;
            if (isPalindrome(reversedWord, 0, reversedWord.length() - 1)) {
                curr.palindromePrefixIndices.add(i);
            }
            for (int j = 0; j < reversedWord.length(); j++) {
                char c = reversedWord.charAt(j);
                if (curr.children[c - 'a'] == null) {
                    curr.children[c - 'a'] = new TrieNode();
                }
                curr = curr.children[c - 'a'];
                if (isPalindrome(reversedWord, j + 1, reversedWord.length() - 1)) {
                    curr.palindromePrefixIndices.add(i);
                }
            }
            curr.wordIndex = i;
        }

        List<List<Integer>> result = new ArrayList<>();
        for (int i = 0; i < words.length; i++) {
            String word = words[i];
            TrieNode curr = root;
            for (int j = 0; j < word.length(); j++) {
                if (curr.wordIndex != -1 && curr.wordIndex != i && isPalindrome(word, j, word.length() - 1)) {
                    result.add(Arrays.asList(i, curr.wordIndex));
                }
                char c = word.charAt(j);
                curr = curr.children[c - 'a'];
                if (curr == null) break;
            }
            if (curr != null) {
                if (curr.wordIndex != -1 && curr.wordIndex != i) {
                    result.add(Arrays.asList(i, curr.wordIndex));
                }
                for (int j : curr.palindromePrefixIndices) {
                    if (i != j) {
                        result.add(Arrays.asList(i, j));
                    }
                }
            }
        }
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(N \cdot K^2)$ với $N$ là số từ, $K$ là độ dài từ lớn nhất.
- **Không gian**: $O(N \cdot K)$ cho việc lưu trữ Trie.

---

## 12. Word Squares (LeetCode 425)

### Đề bài chi tiết
Cho một danh sách các từ có cùng độ dài. Tìm tất cả các "Hình vuông chữ cái" (Word Squares) có thể tạo ra. Hình vuông chữ cái là ma trận mà việc đọc các từ theo hàng ngang bằng với việc đọc theo cột dọc.

### Phân tích thuật toán
Khi xây dựng được một hình vuông tạm thời độ lớn $k$, ta cần tìm từ tiếp theo bắt đầu bằng chuỗi tiền tố là các ký tự ở cột thứ $k$. Sử dụng Trie để lấy tất cả các từ khớp với tiền tố đó cực kỳ nhanh, kết hợp với thuật toán Backtracking.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        List<String> startWith = new ArrayList<>();
    }
    
    TrieNode root = new TrieNode();

    private void buildTrie(String[] words) {
        for (String word : words) {
            TrieNode curr = root;
            for (char c : word.toCharArray()) {
                if (curr.children[c - 'a'] == null) {
                    curr.children[c - 'a'] = new TrieNode();
                }
                curr = curr.children[c - 'a'];
                curr.startWith.add(word);
            }
        }
    }

    private List<String> getWordsWithPrefix(String prefix) {
        TrieNode curr = root;
        for (char c : prefix.toCharArray()) {
            if (curr.children[c - 'a'] == null) return new ArrayList<>();
            curr = curr.children[c - 'a'];
        }
        return curr.startWith;
    }

    public List<List<String>> wordSquares(String[] words) {
        List<List<String>> res = new ArrayList<>();
        if (words == null || words.length == 0) return res;
        buildTrie(words);
        List<String> currentSquare = new ArrayList<>();
        for (String word : words) {
            currentSquare.add(word);
            backtrack(1, words[0].length(), currentSquare, res);
            currentSquare.remove(currentSquare.size() - 1);
        }
        return res;
    }

    private void backtrack(int step, int len, List<String> currentSquare, List<List<String>> res) {
        if (step == len) {
            res.add(new ArrayList<>(currentSquare));
            return;
        }
        StringBuilder prefix = new StringBuilder();
        for (String word : currentSquare) {
            prefix.append(word.charAt(step));
        }
        for (String candidate : getWordsWithPrefix(prefix.toString())) {
            currentSquare.add(candidate);
            backtrack(step + 1, len, currentSquare, res);
            currentSquare.remove(currentSquare.size() - 1);
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(N \cdot 26^L)$ ở trường hợp xấu nhất, cực kỳ phụ thuộc vào Backtracking, với $L$ là độ dài từ.
- **Không gian**: $O(N \cdot L)$ cho bộ nhớ Trie và bộ nhớ đệ quy Stack.

---

## 13. Concatenated Words (LeetCode 472)

### Đề bài chi tiết
Cho một danh sách từ, hãy tìm tất cả các "từ ghép" (concatenated words). Một từ được xem là từ ghép nếu nó có thể được tạo thành từ ít nhất 2 từ khác ngắn hơn tồn tại trong danh sách.

### Phân tích thuật toán
Sắp xếp danh sách các từ theo độ dài tăng dần. Duyệt qua từng từ: kiểm tra xem từ hiện tại có thể được tạo thành từ các từ trước đó (đã lưu trong Trie) hay không bằng cách dùng đệ quy DFS (hoặc Quy hoạch động DP). Nếu không, ta chèn nó vào cây Trie để các từ phía sau dùng.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean isEnd = false;
    }
    
    TrieNode root = new TrieNode();

    public List<String> findAllConcatenatedWordsInADict(String[] words) {
        List<String> res = new ArrayList<>();
        Arrays.sort(words, (a, b) -> a.length() - b.length());
        
        for (String word : words) {
            if (word.length() == 0) continue;
            if (canForm(word, 0)) {
                res.add(word);
            } else {
                addWord(word);
            }
        }
        return res;
    }
    
    private void addWord(String word) {
        TrieNode curr = root;
        for (char c : word.toCharArray()) {
            if (curr.children[c - 'a'] == null) {
                curr.children[c - 'a'] = new TrieNode();
            }
            curr = curr.children[c - 'a'];
        }
        curr.isEnd = true;
    }
    
    private boolean canForm(String word, int index) {
        if (index == word.length()) return true;
        TrieNode curr = root;
        for (int i = index; i < word.length(); i++) {
            if (curr.children[word.charAt(i) - 'a'] == null) return false;
            curr = curr.children[word.charAt(i) - 'a'];
            if (curr.isEnd && canForm(word, i + 1)) return true;
        }
        return false;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(N \log N + N \cdot L^3)$ với $N$ là số từ và $L$ độ dài từ. Hàm `canForm` có thể tốn $O(L^2)$ ở mỗi từ.
- **Không gian**: $O(N \cdot L)$ của cây Trie.

---

## 14. Boggle Game

### Đề bài chi tiết
Đây là bài toán tìm số lượng từ lớn nhất (từ điển cho trước, các từ không trùng) bằng cách đi trên lưới Boggle (được đi các hướng khác nhau liên tiếp). Từ khi được chọn sẽ tốn các ký tự tại lưới, không được dùng lại các ô đó nữa cho từ khác. Yêu cầu trả về số lượng từ lớn nhất có thể tìm thấy.

### Phân tích thuật toán
Đây là một bài toán khó kết hợp Trie và Backtracking. Tại mỗi ô, ta tìm tất cả các từ có thể, "lấy" một từ bằng cách đánh dấu các ô trên đường đi khỏi lưới (thay bằng '#'), rồi tiếp tục đệ quy tìm nhiều nhất có thể từ vị trí hiện tại. So sánh để lấy giá trị tối ưu cực đại.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean isEnd = false;
    }

    private void insert(TrieNode root, String word) {
        TrieNode node = root;
        for (char c : word.toCharArray()) {
            int idx = c - 'a';
            if (node.children[idx] == null) {
                node.children[idx] = new TrieNode();
            }
            node = node.children[idx];
        }
        node.isEnd = true;
    }

    public int boggleGame(char[][] board, String[] words) {
        TrieNode root = new TrieNode();
        for (String word : words) {
            insert(root, word);
        }
        return dfsMax(board, root, 0, 0);
    }

    private int dfsMax(char[][] board, TrieNode root, int r, int c) {
        int m = board.length;
        int n = board[0].length;
        if (r >= m) return 0;
        if (c >= n) return dfsMax(board, root, r + 1, 0);

        int maxWords = dfsMax(board, root, r, c + 1);

        List<List<Integer>> matchedPaths = new ArrayList<>();
        findWords(board, r, c, root, new ArrayList<>(), matchedPaths);

        for (List<Integer> path : matchedPaths) {
            Map<Integer, Character> backup = new HashMap<>();
            for (int pos : path) {
                int pr = pos / n, pc = pos % n;
                backup.put(pos, board[pr][pc]);
                board[pr][pc] = '#';
            }
            
            maxWords = Math.max(maxWords, 1 + dfsMax(board, root, r, c + 1));
            
            for (int pos : path) {
                int pr = pos / n, pc = pos % n;
                board[pr][pc] = backup.get(pos);
            }
        }
        return maxWords;
    }

    private void findWords(char[][] board, int r, int c, TrieNode node, List<Integer> currentPath, List<List<Integer>> matchedPaths) {
        int m = board.length, n = board[0].length;
        if (r < 0 || r >= m || c < 0 || c >= n || board[r][c] == '#') return;
        
        char ch = board[r][c];
        if (node.children[ch - 'a'] == null) return;
        
        node = node.children[ch - 'a'];
        currentPath.add(r * n + c);
        
        if (node.isEnd) {
            matchedPaths.add(new ArrayList<>(currentPath));
        } else {
            board[r][c] = '#';
            int[] dr = {0, 0, 1, -1};
            int[] dc = {1, -1, 0, 0};
            for (int i = 0; i < 4; i++) {
                findWords(board, r + dr[i], c + dc[i], node, currentPath, matchedPaths);
            }
            board[r][c] = ch;
        }
        currentPath.remove(currentPath.size() - 1);
    }
}
```

### Độ phức tạp
- **Thời gian**: Rất lớn, dao động theo cấp số mũ phụ thuộc vào số đường dẫn có thể tìm thấy tại mỗi node.
- **Không gian**: $O(N \cdot L + M \cdot N)$ cho cây Trie và stack đệ quy / danh sách đường đi.

---

## 15. Shortest Unique Prefix

### Đề bài chi tiết
Cho một danh sách các từ. Hãy tìm tiền tố duy nhất ngắn nhất cho mỗi từ trong danh sách, sao cho tiền tố đó không phải là tiền tố của bất kỳ từ nào khác trong danh sách.

### Phân tích thuật toán
Sử dụng cấu trúc Trie. Tại mỗi nút của Trie, lưu trữ biến `count` đếm số lượng từ đi qua nhánh đó. Khi xây dựng xong Trie, ta duyệt lại từng từ: khi duyệt tới một vị trí mà `count == 1` thì chuỗi đó chính là tiền tố duy nhất ngắn nhất cho từ hiện tại.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        int count = 0;
    }
    
    public String[] findShortestPrefixes(String[] words) {
        TrieNode root = new TrieNode();
        for (String word : words) {
            TrieNode curr = root;
            for (char c : word.toCharArray()) {
                if (curr.children[c - 'a'] == null) {
                    curr.children[c - 'a'] = new TrieNode();
                }
                curr = curr.children[c - 'a'];
                curr.count++;
            }
        }
        
        String[] res = new String[words.length];
        for (int i = 0; i < words.length; i++) {
            TrieNode curr = root;
            StringBuilder prefix = new StringBuilder();
            for (char c : words[i].toCharArray()) {
                prefix.append(c);
                curr = curr.children[c - 'a'];
                if (curr.count == 1) {
                    break;
                }
            }
            res[i] = prefix.toString();
        }
        return res;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(N \cdot L)$ để chèn từ và $O(N \cdot L)$ để truy vấn kết quả.
- **Không gian**: $O(N \cdot L)$ cho toàn bộ cây Trie.

---

## 16. Design Search Autocomplete System (LeetCode 642)

### Đề bài chi tiết
Thiết kế hệ thống tự động hoàn thành từ (autocomplete system). Cho danh sách các câu đã tìm kiếm trước đó và tần suất. Hàm `input(char c)` sẽ nhận vào từng ký tự một và trả về top 3 câu có cùng tiền tố hiện tại với lượng tìm kiếm cao nhất (ưu tiên thứ tự từ điển nếu bằng tần suất). Ký tự `#` báo hiệu kết thúc một câu và cập nhật vào hệ thống.

### Phân tích thuật toán
Xây dựng Trie, mỗi Node có HashMap chứa các node con và lưu trữ luôn danh sách các câu đi qua nhánh đó kèm theo tần suất `counts`. Khi truy vấn, duyệt đến Node cuối cùng của `prefix` và dùng PriorityQueue (Max-Heap) để lọc top 3 kết quả từ danh sách `counts`.

### Mã nguồn Java
```java
import java.util.*;

class AutocompleteSystem {
    class TrieNode {
        Map<Character, TrieNode> children = new HashMap<>();
        Map<String, Integer> counts = new HashMap<>();
    }
    
    TrieNode root;
    String prefix;
    
    public AutocompleteSystem(String[] sentences, int[] times) {
        root = new TrieNode();
        prefix = "";
        for (int i = 0; i < sentences.length; i++) {
            add(sentences[i], times[i]);
        }
    }
    
    private void add(String sentence, int count) {
        TrieNode curr = root;
        for (char c : sentence.toCharArray()) {
            curr.children.putIfAbsent(c, new TrieNode());
            curr = curr.children.get(c);
            curr.counts.put(sentence, curr.counts.getOrDefault(sentence, 0) + count);
        }
    }
    
    public List<String> input(char c) {
        if (c == '#') {
            add(prefix, 1);
            prefix = "";
            return new ArrayList<>();
        }
        
        prefix += c;
        TrieNode curr = root;
        for (char ch : prefix.toCharArray()) {
            if (!curr.children.containsKey(ch)) {
                return new ArrayList<>();
            }
            curr = curr.children.get(ch);
        }
        
        PriorityQueue<Map.Entry<String, Integer>> pq = new PriorityQueue<>(
            (a, b) -> (a.getValue().equals(b.getValue()) ? a.getKey().compareTo(b.getKey()) : b.getValue() - a.getValue())
        );
        pq.addAll(curr.counts.entrySet());
        
        List<String> res = new ArrayList<>();
        for (int i = 0; i < 3 && !pq.isEmpty(); i++) {
            res.add(pq.poll().getKey());
        }
        return res;
    }
}
```

### Độ phức tạp
- **Thời gian**: Khởi tạo $O(N \cdot L)$. Mỗi ký tự input truy vấn tốn $O(L + M \log M)$ với $M$ là số câu khớp prefix.
- **Không gian**: $O(N \cdot L)$ lưu trữ thông tin câu và số lượng tại Node.

---

## 17. Maximum XOR With an Element From Array (LeetCode 1707)

### Đề bài chi tiết
Cho mảng số nguyên `nums` và danh sách các truy vấn, trong đó `queries[i] = [xi, mi]`. Trả về mảng đáp án, trong đó phần tử thứ $i$ là giá trị XOR lớn nhất giữa $xi$ và phần tử bất kỳ thuộc `nums` mà $\le mi$. Nếu không có số nào thỏa mãn, trả về -1.

### Phân tích thuật toán
Lưu truy vấn offline: thêm index thực vào cấu trúc truy vấn, sau đó sort theo giá trị $mi$ tăng dần. Sort cả mảng `nums`. Lặp qua từng truy vấn, chèn dần các số từ `nums` mà $\le mi$ vào Bitwise Trie. Tìm `maxXOR` trong Trie bằng cách đi đảo ngược bit.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[2];
    }
    
    private void insert(TrieNode root, int num) {
        TrieNode curr = root;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if (curr.children[bit] == null) curr.children[bit] = new TrieNode();
            curr = curr.children[bit];
        }
    }
    
    private int getMaxXor(TrieNode root, int num) {
        if (root.children[0] == null && root.children[1] == null) return -1;
        TrieNode curr = root;
        int maxXor = 0;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            int toggle = 1 - bit;
            if (curr.children[toggle] != null) {
                maxXor |= (1 << i);
                curr = curr.children[toggle];
            } else {
                curr = curr.children[bit];
            }
        }
        return maxXor;
    }
    
    public int[] maximizeXor(int[] nums, int[][] queries) {
        Arrays.sort(nums);
        int n = queries.length;
        int[][] qWithIndex = new int[n][3];
        for (int i = 0; i < n; i++) {
            qWithIndex[i][0] = queries[i][0];
            qWithIndex[i][1] = queries[i][1];
            qWithIndex[i][2] = i;
        }
        Arrays.sort(qWithIndex, (a, b) -> a[1] - b[1]);
        
        int[] result = new int[n];
        TrieNode root = new TrieNode();
        int j = 0;
        
        for (int[] q : qWithIndex) {
            int xi = q[0], mi = q[1], index = q[2];
            while (j < nums.length && nums[j] <= mi) {
                insert(root, nums[j]);
                j++;
            }
            result[index] = getMaxXor(root, xi);
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian**: Sắp xếp $O(N \log N + Q \log Q)$. Chèn và xử lý truy vấn $O((N + Q) \cdot 32)$. Tổng $O(N \log N + Q \log Q)$.
- **Không gian**: $O(N \cdot 32 + Q)$ do bitwise Trie.

---

## 18. Multi-Search / Aho-Corasick

### Đề bài chi tiết
Cho một chuỗi lớn `text` và danh sách các chuỗi mẫu `words`. Hãy tìm tất cả các vị trí xuất hiện của mỗi từ trong `words` bên trong `text`.

### Phân tích thuật toán
Tạo Trie chuẩn từ danh sách `words`, lưu `wordIndex` tại điểm kết thúc. Duyệt từ từng vị trí $i$ trong `text` và đi sâu vào Trie để tìm từ khớp, nếu khớp thì ghi nhận vị trí vào mảng kết quả của từ đó. (Giải pháp Trie truyền thống đáp ứng tốt bài này).

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        int wordIndex = -1;
    }
    
    public int[][] multiSearch(String big, String[] smalls) {
        TrieNode root = new TrieNode();
        for (int i = 0; i < smalls.length; i++) {
            if (smalls[i].isEmpty()) continue;
            TrieNode curr = root;
            for (char c : smalls[i].toCharArray()) {
                if (curr.children[c - 'a'] == null) curr.children[c - 'a'] = new TrieNode();
                curr = curr.children[c - 'a'];
            }
            curr.wordIndex = i;
        }
        
        List<Integer>[] resList = new List[smalls.length];
        for (int i = 0; i < smalls.length; i++) resList[i] = new ArrayList<>();
        
        for (int i = 0; i < big.length(); i++) {
            TrieNode curr = root;
            for (int j = i; j < big.length(); j++) {
                char c = big.charAt(j);
                if (curr.children[c - 'a'] == null) break;
                curr = curr.children[c - 'a'];
                if (curr.wordIndex != -1) {
                    resList[curr.wordIndex].add(i);
                }
            }
        }
        
        int[][] res = new int[smalls.length][];
        for (int i = 0; i < smalls.length; i++) {
            res[i] = new int[resList[i].size()];
            for (int k = 0; k < resList[i].size(); k++) res[i][k] = resList[i].get(k);
        }
        return res;
    }
}
```

### Độ phức tạp
- **Thời gian**: Xây dựng Trie $O(K \cdot L)$ với $K$ là số từ, $L$ là chiều dài lớn nhất. Tìm kiếm tốn $O(M^2)$ với $M$ là chiều dài văn bản (chấp nhận được với phỏng vấn thông thường).
- **Không gian**: $O(K \cdot L)$ lưu trữ cây Trie.

---

## 19. Count Valid Words Given a Prefix

### Đề bài chi tiết
Bạn có một từ điển. Yêu cầu xử lý nhiều truy vấn: cho một chuỗi prefix, trả về số lượng từ trong từ điển có tiền tố là prefix đó.

### Phân tích thuật toán
Dùng biến `count` ở mỗi `TrieNode`. Khi insert một từ mới, ta đi qua các Node và luôn tăng biến `count` của Node đó lên 1. Khi truy vấn prefix, chỉ cần duyệt đến Node cuối của prefix và trả về giá trị `count`.

### Mã nguồn Java
```java
class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        int count = 0;
    }
    
    TrieNode root = new TrieNode();

    public void insert(String word) {
        TrieNode curr = root;
        for (char c : word.toCharArray()) {
            if (curr.children[c - 'a'] == null) {
                curr.children[c - 'a'] = new TrieNode();
            }
            curr = curr.children[c - 'a'];
            curr.count++;
        }
    }
    
    public int countPrefix(String prefix) {
        TrieNode curr = root;
        for (char c : prefix.toCharArray()) {
            if (curr.children[c - 'a'] == null) return 0;
            curr = curr.children[c - 'a'];
        }
        return curr.count;
    }
}
```

### Độ phức tạp
- **Thời gian**: Chèn từ $O(L)$, truy vấn $O(L)$ với $L$ là độ dài chuỗi.
- **Không gian**: $O(N \cdot L)$ tổng dung lượng cấu trúc.

---

## 20. Remove Sub-Folders from the Filesystem (LeetCode 1233)

### Đề bài chi tiết
Cho mảng các đường dẫn folder (ví dụ `/a/b`, `/a/b/c`, `/d`). Trả về danh sách các folder sau khi đã loại bỏ các folder con (ví dụ `/a/b/c` bị xóa vì nằm trong `/a/b`).

### Phân tích thuật toán
Sử dụng Trie. Phân tách đường dẫn thư mục bằng ký tự `/`. Khi chèn vào Trie, nếu trên đường đi gặp một Node đánh dấu `isEnd == true` (nghĩa là đã gặp thư mục cha), ta ngừng và không chèn các thư mục con vào nữa. Duyệt DFS cuối cùng để lấy các đường dẫn kết quả.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    class TrieNode {
        Map<String, TrieNode> children = new HashMap<>();
        boolean isEnd = false;
        String path = null;
    }
    
    public List<String> removeSubfolders(String[] folder) {
        TrieNode root = new TrieNode();
        for (String f : folder) {
            String[] parts = f.split("/");
            TrieNode curr = root;
            for (int i = 1; i < parts.length; i++) {
                String p = parts[i];
                if (!curr.children.containsKey(p)) {
                    curr.children.put(p, new TrieNode());
                }
                curr = curr.children.get(p);
            }
            curr.isEnd = true;
            curr.path = f;
        }
        
        List<String> res = new ArrayList<>();
        dfs(root, res);
        return res;
    }
    
    private void dfs(TrieNode node, List<String> res) {
        if (node.isEnd) {
            res.add(node.path);
            return;
        }
        for (TrieNode child : node.children.values()) {
            dfs(child, res);
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(N \cdot L)$ split chuỗi và chèn vào Trie, với $L$ là độ sâu của thư mục.
- **Không gian**: $O(N \cdot L)$ cho toàn bộ cây thư mục.

---

## 21. Subarrays with XOR Less Than K

### Đề bài chi tiết
Cho một mảng các số nguyên và một số nguyên `k`. Hãy đếm số lượng các mảng con liên tiếp mà giá trị XOR của tất cả các phần tử trong mảng con đó nhỏ hơn `k`.

### Phân tích thuật toán
Sử dụng mảng cộng dồn XOR (Prefix XOR). Giá trị XOR của mảng con từ `i` đến `j` chính là `prefix[i-1] ^ prefix[j]`. Bài toán quy về: Với mỗi `prefix[j]`, tìm số lượng `prefix[i]` (với `i < j`) sao cho `prefix[i] ^ prefix[j] < k`.
Ta dùng một Bitwise Trie. Lưu các giá trị `prefix` trước đó vào Trie, mỗi node lưu biến `count`. Khi duyệt tìm các số XOR với `prefix[j]` mà `< k`, nếu bit của `k` là 1, ta cộng toàn bộ nhánh tạo ra bit 0 (vì chắc chắn bé hơn), rồi đi tiếp vào nhánh tạo ra bit 1. Nếu bit của `k` là 0, ta bắt buộc phải đi vào nhánh tạo ra bit 0.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[2];
        int count = 0;
    }
    
    private void insert(TrieNode root, int val) {
        TrieNode curr = root;
        for (int i = 31; i >= 0; i--) {
            int bit = (val >> i) & 1;
            if (curr.children[bit] == null) {
                curr.children[bit] = new TrieNode();
            }
            curr = curr.children[bit];
            curr.count++;
        }
    }
    
    private int countLess(TrieNode root, int val, int high) {
        TrieNode curr = root;
        int res = 0;
        for (int i = 31; i >= 0 && curr != null; i--) {
            int bitV = (val >> i) & 1;
            int bitH = (high >> i) & 1;
            if (bitH == 1) {
                if (curr.children[bitV] != null) {
                    res += curr.children[bitV].count;
                }
                curr = curr.children[1 - bitV];
            } else {
                curr = curr.children[bitV];
            }
        }
        return res;
    }

    public int subarraysWithXorLessThanK(int[] arr, int k) {
        TrieNode root = new TrieNode();
        insert(root, 0);
        int res = 0, pre = 0;
        for (int num : arr) {
            pre ^= num;
            res += countLess(root, pre, k);
            insert(root, pre);
        }
        return res;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(N \cdot 32)$. Mỗi phần tử duyệt 32 bit.
- **Không gian**: $O(N \cdot 32)$ cho Bitwise Trie.

---

## 22. Maximum Genetic Difference Query

### Đề bài chi tiết
Bạn được cung cấp một đồ thị cây (có gốc). Có danh sách các truy vấn dạng `(node, val)`. Đối với mỗi truy vấn, hãy tìm giá trị XOR lớn nhất giữa `val` và một giá trị của một node bất kỳ nằm trên đường đi từ gốc đến `node` đó.

### Phân tích thuật toán
Dùng thuật toán Duyệt theo chiều sâu (DFS) trên cây và cấu trúc Bitwise Trie. Khi đi xuống một nhánh (thăm node), ta chèn giá trị của node vào Trie. Sau đó giải quyết toàn bộ các truy vấn liên quan đến node hiện tại (tìm Max XOR tương tự bài 4). Khi thoát khỏi hàm DFS (để lui về parent), ta "xóa" giá trị của node khỏi Trie (bằng cách giảm biến `count` của node trên nhánh Trie đi 1) để không ảnh hưởng đến các nhánh khác.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[2];
        int count = 0;
    }
    
    TrieNode root = new TrieNode();
    
    private void insert(int val, int d) {
        TrieNode curr = root;
        for (int i = 17; i >= 0; i--) {
            int bit = (val >> i) & 1;
            if (curr.children[bit] == null) curr.children[bit] = new TrieNode();
            curr = curr.children[bit];
            curr.count += d;
        }
    }
    
    private int getMax(int val) {
        TrieNode curr = root;
        int res = 0;
        for (int i = 17; i >= 0; i--) {
            int bit = (val >> i) & 1;
            int toggle = 1 - bit;
            if (curr.children[toggle] != null && curr.children[toggle].count > 0) {
                res |= (1 << i);
                curr = curr.children[toggle];
            } else {
                curr = curr.children[bit];
            }
        }
        return res;
    }
    
    public int[] maxGeneticDifference(int[] parents, int[][] queries) {
        int n = parents.length;
        List<Integer>[] tree = new List[n];
        for (int i = 0; i < n; i++) tree[i] = new ArrayList<>();
        int rootNode = -1;
        for (int i = 0; i < n; i++) {
            if (parents[i] == -1) rootNode = i;
            else tree[parents[i]].add(i);
        }
        
        List<int[]>[] qList = new List[n];
        for (int i = 0; i < n; i++) qList[i] = new ArrayList<>();
        for (int i = 0; i < queries.length; i++) {
            qList[queries[i][0]].add(new int[]{queries[i][1], i});
        }
        
        int[] res = new int[queries.length];
        dfs(rootNode, tree, qList, res);
        return res;
    }
    
    private void dfs(int node, List<Integer>[] tree, List<int[]>[] qList, int[] res) {
        insert(node, 1);
        for (int[] q : qList[node]) res[q[1]] = getMax(q[0]);
        for (int child : tree[node]) dfs(child, tree, qList, res);
        insert(node, -1); // backtracking
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(N \cdot 18 + Q \cdot 18)$ với $N$ là số node và $Q$ là số truy vấn (giới hạn giá trị $10^5 \approx 2^{17}$).
- **Không gian**: $O(N \cdot 18 + Q)$ bộ nhớ cấp phát.

---

## 23. Number of Matching Subsequences (LeetCode 792)

### Đề bài chi tiết
Cho chuỗi `s` và một mảng các chuỗi `words`. Trả về số lượng `words[i]` là chuỗi con (subsequence) của chuỗi `s`.

### Phân tích thuật toán
Thay vì xây dựng Trie truyền thống, ta mô phỏng trạng thái đi trên "các nhánh tưởng tượng". Tạo ra 26 bucket. Khi gặp một ký tự của chuỗi con cần khớp, ta cho chúng vào bucket của chữ cái bắt đầu. Duyệt `s` từ trái qua phải, gặp ký tự nào thì ta lấy toàn bộ các chuỗi con trong bucket đó ra, di chuyển index của chúng lên 1 (đi tiếp), và phân phát lại vào các bucket kế tiếp. 

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    class Node {
        String word;
        int index;
        public Node(String w, int i) {
            word = w;
            index = i;
        }
    }
    
    public int numMatchingSubseq(String s, String[] words) {
        List<Node>[] buckets = new List[26];
        for (int i = 0; i < 26; i++) buckets[i] = new ArrayList<>();
        
        for (String word : words) {
            buckets[word.charAt(0) - 'a'].add(new Node(word, 0));
        }
        
        int res = 0;
        for (char c : s.toCharArray()) {
            List<Node> currentBucket = buckets[c - 'a'];
            buckets[c - 'a'] = new ArrayList<>();
            for (Node node : currentBucket) {
                node.index++;
                if (node.index == node.word.length()) {
                    res++;
                } else {
                    buckets[node.word.charAt(node.index) - 'a'].add(node);
                }
            }
        }
        return res;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(L + \sum K_i)$ với $L$ là độ dài `s` và $K_i$ là độ dài các `words`.
- **Không gian**: $O(W)$ với $W$ là số lượng các `words`.

---

## 24. Design In-Memory File System (LeetCode 588)

### Đề bài chi tiết
Thiết kế hệ thống tệp tin trong bộ nhớ. Triển khai các hàm `ls`, `mkdir`, `addContentToFile`, và `readContentFromFile`. Mỗi cấp thư mục được lưu theo cấp bậc.

### Phân tích thuật toán
Sử dụng mô hình Trie, trong đó mỗi `TrieNode` đại diện cho một thư mục hoặc một tệp (tách biệt qua thuộc tính `isFile`). Mảng con được thay thế bằng một `TreeMap` (vì `ls` yêu cầu trả về theo thứ tự từ điển) trỏ từ Tên -> Node. Các lệnh duyệt file tương tự duyệt Trie với từng thành phần sau khi `split("/")`.

### Mã nguồn Java
```java
import java.util.*;

class FileSystem {
    class FileNode {
        boolean isFile;
        StringBuilder content = new StringBuilder();
        Map<String, FileNode> children = new TreeMap<>();
    }
    
    FileNode root;

    public FileSystem() {
        root = new FileNode();
    }
    
    public List<String> ls(String path) {
        FileNode curr = root;
        if (!path.equals("/")) {
            String[] parts = path.split("/");
            for (int i = 1; i < parts.length; i++) {
                curr = curr.children.get(parts[i]);
            }
            if (curr.isFile) {
                return Arrays.asList(parts[parts.length - 1]);
            }
        }
        return new ArrayList<>(curr.children.keySet());
    }
    
    public void mkdir(String path) {
        FileNode curr = root;
        String[] parts = path.split("/");
        for (int i = 1; i < parts.length; i++) {
            curr.children.putIfAbsent(parts[i], new FileNode());
            curr = curr.children.get(parts[i]);
        }
    }
    
    public void addContentToFile(String filePath, String content) {
        FileNode curr = root;
        String[] parts = filePath.split("/");
        for (int i = 1; i < parts.length; i++) {
            curr.children.putIfAbsent(parts[i], new FileNode());
            curr = curr.children.get(parts[i]);
        }
        curr.isFile = true;
        curr.content.append(content);
    }
    
    public String readContentFromFile(String filePath) {
        FileNode curr = root;
        String[] parts = filePath.split("/");
        for (int i = 1; i < parts.length; i++) {
            curr = curr.children.get(parts[i]);
        }
        return curr.content.toString();
    }
}
```

### Độ phức tạp
- **Thời gian**: Phụ thuộc chiều sâu thư mục $O(H \log K)$ với $H$ độ sâu và $K$ là số lượng con (do `TreeMap`).
- **Không gian**: Bộ nhớ phụ thuộc kích thước dữ liệu chèn.

---

## 25. Find All Good Strings (LeetCode 1397)

### Đề bài chi tiết
Cho hai chuỗi `s1` và `s2` có cùng độ dài `n`, và chuỗi `evil`. Tìm số lượng "chuỗi tốt" (Good Strings) theo thứ tự từ điển nằm giữa `s1` và `s2` (bao gồm) không chứa chuỗi `evil` như một chuỗi con. Trả về kết quả module $10^9 + 7$.

### Phân tích thuật toán
Bài toán siêu kết hợp giữa Digit DP, KMP và khái niệm khớp tiền tố chuỗi. KMP được sử dụng tương đương với DFA / Trie rút gọn để kiểm tra số ký tự đã "so khớp" với chuỗi `evil`. DFS tính DP bằng cách chọn ký tự từ biên này đến biên nọ, lấy lps (Longest Prefix Suffix) để xem đi tới state nào của `evil`.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    int MOD = 1000000007;

    public int findGoodStrings(int n, String s1, String s2, String evil) {
        int[] lps = computeLPS(evil);
        int[][][][] dp = new int[n][evil.length()][2][2];
        for (int[][][] d1 : dp) for (int[][] d2 : d1) for (int[] d3 : d2) Arrays.fill(d3, -1);
        return dfs(0, 0, 1, 1, n, s1, s2, evil, lps, dp);
    }
    
    private int[] computeLPS(String s) {
        int[] lps = new int[s.length()];
        int j = 0;
        for (int i = 1; i < s.length(); i++) {
            while (j > 0 && s.charAt(i) != s.charAt(j)) j = lps[j - 1];
            if (s.charAt(i) == s.charAt(j)) j++;
            lps[i] = j;
        }
        return lps;
    }
    
    private int dfs(int idx, int evilMatched, int isB1, int isB2, 
                    int n, String s1, String s2, String evil, int[] lps, int[][][][] dp) {
        if (evilMatched == evil.length()) return 0;
        if (idx == n) return 1;
        
        if (dp[idx][evilMatched][isB1][isB2] != -1) return dp[idx][evilMatched][isB1][isB2];
        
        char from = isB1 == 1 ? s1.charAt(idx) : 'a';
        char to = isB2 == 1 ? s2.charAt(idx) : 'z';
        int res = 0;
        
        for (char c = from; c <= to; c++) {
            int j = evilMatched;
            while (j > 0 && c != evil.charAt(j)) j = lps[j - 1];
            if (c == evil.charAt(j)) j++;
            
            int nextB1 = (isB1 == 1 && c == from) ? 1 : 0;
            int nextB2 = (isB2 == 1 && c == to) ? 1 : 0;
            res = (res + dfs(idx + 1, j, nextB1, nextB2, n, s1, s2, evil, lps, dp)) % MOD;
        }
        
        return dp[idx][evilMatched][isB1][isB2] = res;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(N \cdot K \cdot 2 \cdot 2 \cdot 26)$ với $N$ là độ dài, $K$ chiều dài `evil`.
- **Không gian**: $O(N \cdot K \cdot 4)$.

---

## 26. Lexicographical Numbers (LeetCode 386)

### Đề bài chi tiết
Cho số nguyên `n`. In ra mảng số từ `1` tới `n` nhưng được sắp xếp theo thứ tự từ điển (Lexicographical). Không được dùng hàm Sort chuỗi thông thường do yêu cầu $O(N)$ thời gian.

### Phân tích thuật toán
Hãy coi mỗi chữ số tương đương với việc đi sâu vào các nhánh của cây Trie. Bắt đầu từ Node ảo, ta có thể rẽ vào nhánh `1-9` (không có 0). Từ một nhánh `curr`, ta có thể rẽ tiếp vào `0-9` tương ứng với nhân thêm 10 và cộng chữ số đó. DFS bản chất là nhân 10; quay lui bản chất là chia 10.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    public List<Integer> lexicalOrder(int n) {
        List<Integer> res = new ArrayList<>();
        int curr = 1;
        for (int i = 0; i < n; i++) {
            res.add(curr);
            if (curr * 10 <= n) {
                curr *= 10;
            } else {
                while (curr % 10 == 9 || curr + 1 > n) {
                    curr /= 10;
                }
                curr++;
            }
        }
        return res;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(N)$. Mỗi số được lấy chính xác 1 lần, những lần tăng giảm cũng bù trừ vòng lặp.
- **Không gian**: $O(1)$ nếu không tính mảng `res` đầu ra.

---

## 27. Count Pairs With XOR in a Range (LeetCode 1803)

### Đề bài chi tiết
Cho một mảng nguyên `nums` và hai số nguyên `low`, `high`. Trả về số lượng các cặp `(i, j)` với `i < j` sao cho `low <= (nums[i] XOR nums[j]) <= high`.

### Phân tích thuật toán
Bản chất là sử dụng bài toán 21: Đếm số lượng cặp có XOR nhỏ hơn `k`. Để tính trong đoạn $[low, high]$, ta dùng quy tắc hàm: `countPairs(high + 1) - countPairs(low)`. Bitwise Trie giải quyết hoàn hảo với độ phức tạp $O(N \cdot 32)$.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[2];
        int count = 0;
    }
    
    private void insert(TrieNode root, int val) {
        TrieNode curr = root;
        for (int i = 14; i >= 0; i--) {
            int bit = (val >> i) & 1;
            if (curr.children[bit] == null) curr.children[bit] = new TrieNode();
            curr = curr.children[bit];
            curr.count++;
        }
    }
    
    private int countLess(TrieNode root, int val, int limit) {
        TrieNode curr = root;
        int res = 0;
        for (int i = 14; i >= 0 && curr != null; i--) {
            int bitV = (val >> i) & 1;
            int bitL = (limit >> i) & 1;
            if (bitL == 1) {
                if (curr.children[bitV] != null) res += curr.children[bitV].count;
                curr = curr.children[1 - bitV];
            } else {
                curr = curr.children[bitV];
            }
        }
        return res;
    }

    public int countPairs(int[] nums, int low, int high) {
        TrieNode root = new TrieNode();
        int res = 0;
        for (int num : nums) {
            res += countLess(root, num, high + 1) - countLess(root, num, low);
            insert(root, num);
        }
        return res;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(N \cdot 15)$ do giới hạn của `nums[i]` là $20000 \approx 2^{14}$.
- **Không gian**: $O(N \cdot 15)$.

---

## 28. Search Suggestions System (LeetCode 1268)

### Đề bài chi tiết
Cho danh sách các `products` và một từ khóa `searchWord`. Thiết kế hệ thống tự động gợi ý tới 3 sản phẩm có chung tiền tố, sau mỗi lần gõ từng ký tự của `searchWord`. Các gợi ý được ưu tiên theo thứ tự từ điển.

### Phân tích thuật toán
Sắp xếp mảng `products` trước. Sau đó dùng Trie lưu trữ các từ. Ở mỗi `TrieNode`, tạo một `List<String>` lưu tối đa 3 từ đi qua nhánh đó. Khi gõ một ký tự của `searchWord`, ta di chuyển dọc theo Trie và trả ra danh sách 3 từ (đã lưu sẵn).

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        List<String> words = new ArrayList<>();
    }
    
    public List<List<String>> suggestedProducts(String[] products, String searchWord) {
        Arrays.sort(products);
        TrieNode root = new TrieNode();
        for (String p : products) {
            TrieNode curr = root;
            for (char c : p.toCharArray()) {
                if (curr.children[c - 'a'] == null) curr.children[c - 'a'] = new TrieNode();
                curr = curr.children[c - 'a'];
                if (curr.words.size() < 3) curr.words.add(p);
            }
        }
        
        List<List<String>> res = new ArrayList<>();
        TrieNode curr = root;
        for (char c : searchWord.toCharArray()) {
            if (curr != null) curr = curr.children[c - 'a'];
            res.add(curr == null ? new ArrayList<>() : curr.words);
        }
        return res;
    }
}
```

### Độ phức tạp
- **Thời gian**: Sorting tốn $O(N \log N \cdot L)$. Build Trie tốn $O(N \cdot L)$.
- **Không gian**: $O(N \cdot L)$ cho cây Trie có List kích thước 3.

---

## 29. Camelcase Matching (LeetCode 1023)

### Đề bài chi tiết
Cho mảng `queries` và `pattern`. Một chuỗi khớp với pattern nếu ta có thể chèn các chữ cái in thường vào pattern để nó giống y hệt chuỗi truy vấn (tuyệt đối không được chèn chữ HOA). Trả về boolean array tương ứng.

### Phân tích thuật toán
Mặc dù có thể tạo Trie để so khớp hàng loạt nếu pattern là nhiều, trong thực tế chỉ có một `pattern`, nên ta dùng 2 con trỏ `i` (trên query) và `j` (trên pattern). Đi qua từng ký tự của query: nếu khớp với ký tự tại `j` thì `j++`. Nếu không khớp mà lại là chữ HOA thì `false`. Kết thúc phải duyệt hết `pattern`. Trạng thái này là nguyên lý cơ bản của một "Trie matching engine" cho CamelCase.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    public List<Boolean> camelMatch(String[] queries, String pattern) {
        List<Boolean> res = new ArrayList<>();
        for (String q : queries) {
            res.add(isMatch(q, pattern));
        }
        return res;
    }
    
    private boolean isMatch(String query, String pattern) {
        int j = 0;
        for (int i = 0; i < query.length(); i++) {
            char c = query.charAt(i);
            if (j < pattern.length() && c == pattern.charAt(j)) {
                j++;
            } else if (Character.isUpperCase(c)) {
                return false;
            }
        }
        return j == pattern.length();
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(N \cdot L)$ với $N$ là số từ truy vấn và $L$ là chiều dài.
- **Không gian**: $O(1)$.

---

## 30. Longest Common Prefix (LeetCode 14)

### Đề bài chi tiết
Viết một hàm để tìm chuỗi tiền tố chung dài nhất (Longest Common Prefix) trong một mảng các chuỗi. Nếu không có tiền tố chung, trả về chuỗi rỗng `""`.

### Phân tích thuật toán
Sử dụng Trie, chèn tất cả các chuỗi vào Trie. Mỗi nhánh nếu là ký tự chung, nó phải là Node con DUY NHẤT và Node hiện tại KHÔNG PHẢI là điểm kết thúc của một chuỗi. Ta đi qua các node thoả điều kiện đó, ghép thành kết quả tiền tố dài nhất.

### Mã nguồn Java
```java
class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        int numChildren = 0;
        boolean isEnd = false;
    }
    
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) return "";
        TrieNode root = new TrieNode();
        for (String str : strs) {
            if (str.isEmpty()) return "";
            TrieNode curr = root;
            for (char c : str.toCharArray()) {
                if (curr.children[c - 'a'] == null) {
                    curr.children[c - 'a'] = new TrieNode();
                    curr.numChildren++;
                }
                curr = curr.children[c - 'a'];
            }
            curr.isEnd = true;
        }
        
        StringBuilder lcp = new StringBuilder();
        TrieNode curr = root;
        while (curr.numChildren == 1 && !curr.isEnd) {
            for (int i = 0; i < 26; i++) {
                if (curr.children[i] != null) {
                    lcp.append((char) ('a' + i));
                    curr = curr.children[i];
                    break;
                }
            }
        }
        return lcp.toString();
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(S)$ với $S$ là tổng số lượng ký tự của tất cả các chuỗi.
- **Không gian**: $O(S)$ để lưu Trie.
