# Trie (Prefix Tree) - Danh sách bài tập

Danh sách bài tập này được thiết kế để rèn luyện kỹ năng sử dụng cấu trúc dữ liệu Trie (Prefix Tree).

## 1. Implement Trie (Prefix Tree)

**Đề bài chi tiết:**
Thiết kế một cấu trúc dữ liệu Trie (cây tiền tố) hỗ trợ các thao tác sau:
- `Trie()`: Khởi tạo đối tượng.
- `void insert(String word)`: Chèn chuỗi `word` vào Trie.
- `boolean search(String word)`: Trả về `true` nếu chuỗi `word` tồn tại trong Trie, ngược lại `false`.
- `boolean startsWith(String prefix)`: Trả về `true` nếu có bất kỳ chuỗi nào trong Trie bắt đầu bằng tiền tố `prefix`.

**Phân tích thuật toán:**
Mỗi node của Trie sẽ chứa một mảng (hoặc HashMap) các liên kết đến các node con (tương ứng với 26 chữ cái tiếng Anh in thường) và một cờ `isEndOfWord` để nhận diện kết thúc của một từ. Thao tác insert duyệt qua các ký tự của từ, thêm node nếu chưa có. Thao tác search và startsWith duyệt tương tự, search thì yêu cầu `isEndOfWord` phải là true, trong khi startsWith không cần.

**Mã nguồn Java:**
```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
    boolean isEndOfWord = false;
}

public class Trie {
    private TrieNode root;

    public Trie() {
        root = new TrieNode();
    }
    
    public void insert(String word) {
        TrieNode node = root;
        for (char c : word.toCharArray()) {
            int i = c - 'a';
            if (node.children[i] == null) {
                node.children[i] = new TrieNode();
            }
            node = node.children[i];
        }
        node.isEndOfWord = true;
    }
    
    public boolean search(String word) {
        TrieNode node = searchPrefix(word);
        return node != null && node.isEndOfWord;
    }
    
    public boolean startsWith(String prefix) {
        return searchPrefix(prefix) != null;
    }
    
    private TrieNode searchPrefix(String prefix) {
        TrieNode node = root;
        for (char c : prefix.toCharArray()) {
            int i = c - 'a';
            if (node.children[i] == null) return null;
            node = node.children[i];
        }
        return node;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(L)$ cho mỗi thao tác (với $L$ là độ dài của từ).
- Không gian: $O(N \times L)$ cho tổng số các từ chèn vào.

---

## 2. Design Add and Search Words Data Structure

**Đề bài chi tiết:**
Thiết kế cấu trúc dữ liệu hỗ trợ thêm từ mới và tìm kiếm xem một từ (có thể chứa ký tự '.') có khớp với bất kỳ từ nào đã thêm trước đó không. Ký tự '.' có thể thay thế cho bất kỳ ký tự nào.

**Phân tích thuật toán:**
Dùng cấu trúc Trie thông thường để thêm (`addWord`). Khi tìm kiếm (`search`), nếu gặp ký tự thường, duyệt như Trie thông thường. Nếu gặp ký tự '.', ta phải duyệt đệ quy qua tất cả các con không null của node hiện tại.

**Mã nguồn Java:**
```java
class WordDictionary {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean isEndOfWord = false;
    }
    
    private TrieNode root;

    public WordDictionary() {
        root = new TrieNode();
    }
    
    public void addWord(String word) {
        TrieNode node = root;
        for (char c : word.toCharArray()) {
            int i = c - 'a';
            if (node.children[i] == null) node.children[i] = new TrieNode();
            node = node.children[i];
        }
        node.isEndOfWord = true;
    }
    
    public boolean search(String word) {
        return searchHelper(word, 0, root);
    }
    
    private boolean searchHelper(String word, int index, TrieNode node) {
        if (index == word.length()) return node.isEndOfWord;
        char c = word.charAt(index);
        
        if (c == '.') {
            for (TrieNode child : node.children) {
                if (child != null && searchHelper(word, index + 1, child)) {
                    return true;
                }
            }
            return false;
        } else {
            int i = c - 'a';
            if (node.children[i] == null) return false;
            return searchHelper(word, index + 1, node.children[i]);
        }
    }
}
```

**Độ phức tạp:**
- Thời gian: `addWord` là $O(L)$. `search` có thể tốn $O(26^L)$ ở worst case khi chuỗi chỉ toàn `.`
- Không gian: $O(N \times L)$ với $N$ là số lượng từ, $L$ là độ dài trung bình.

---

## 3. Word Search II

**Đề bài chi tiết:**
Cho một bảng lưới 2D chứa các ký tự và một danh sách các từ. Hãy tìm tất cả các từ trong lưới. Các từ có thể được tạo thành bằng cách nối các ô liền kề (ngang hoặc dọc). Không được sử dụng cùng một ô nhiều lần cho một từ.

**Phân tích thuật toán:**
Xây dựng Trie chứa tất cả các từ trong danh sách. Sau đó dùng Backtracking (DFS) qua mỗi ô của lưới. Mỗi bước DFS, kiểm tra xem prefix hiện tại có nằm trong Trie không. Trie giúp tối ưu việc cắt tỉa (pruning): Nếu chuỗi không có tiền tố tương ứng trong Trie, dừng DFS ngay tại đó.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        String word = null;
    }
    
    public List<String> findWords(char[][] board, String[] words) {
        TrieNode root = new TrieNode();
        for (String w : words) {
            TrieNode node = root;
            for (char c : w.toCharArray()) {
                if (node.children[c - 'a'] == null) node.children[c - 'a'] = new TrieNode();
                node = node.children[c - 'a'];
            }
            node.word = w;
        }
        
        List<String> res = new ArrayList<>();
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                dfs(board, i, j, root, res);
            }
        }
        return res;
    }
    
    private void dfs(char[][] board, int i, int j, TrieNode p, List<String> res) {
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length || board[i][j] == '#' || p.children[board[i][j] - 'a'] == null) {
            return;
        }
        
        char c = board[i][j];
        p = p.children[c - 'a'];
        if (p.word != null) {
            res.add(p.word);
            p.word = null; // de-duplicate
        }
        
        board[i][j] = '#';
        dfs(board, i - 1, j, p, res);
        dfs(board, i + 1, j, p, res);
        dfs(board, i, j - 1, p, res);
        dfs(board, i, j + 1, p, res);
        board[i][j] = c;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(M \times N \times 4^L)$ với $M \times N$ là kích thước lưới, $L$ là độ dài cực đại của từ.
- Không gian: $O(W)$ với $W$ là tổng độ dài các từ trong dictionary cho Trie.

---

## 4. Replace Words

**Đề bài chi tiết:**
Cho một từ điển chứa các "gốc" (roots) và một câu. Bạn cần thay thế các từ trong câu bằng gốc ngắn nhất có trong từ điển sao cho gốc đó là tiền tố của từ đó.

**Phân tích thuật toán:**
Đưa tất cả các từ gốc (roots) vào Trie. Duyệt qua từng từ trong câu, sử dụng hàm search đặc biệt trên Trie để tìm tiền tố ngắn nhất là một "gốc". Gặp cờ `isEndOfWord` đầu tiên trên đường đi thì dừng và trả về chuỗi gốc.

**Mã nguồn Java:**
```java
import java.util.List;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        String word;
    }
    
    public String replaceWords(List<String> dictionary, String sentence) {
        TrieNode root = new TrieNode();
        for (String word : dictionary) {
            TrieNode node = root;
            for (char c : word.toCharArray()) {
                if (node.children[c - 'a'] == null) node.children[c - 'a'] = new TrieNode();
                node = node.children[c - 'a'];
            }
            node.word = word;
        }
        
        StringBuilder ans = new StringBuilder();
        for (String word : sentence.split(" ")) {
            if (ans.length() > 0) ans.append(" ");
            
            TrieNode node = root;
            for (char c : word.toCharArray()) {
                if (node.children[c - 'a'] == null || node.word != null) break;
                node = node.children[c - 'a'];
            }
            ans.append(node.word != null ? node.word : word);
        }
        return ans.toString();
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N + S)$ với $N$ là tổng chiều dài các kí tự trong dictionary, $S$ là chiều dài của câu.
- Không gian: $O(N)$ bộ nhớ cho Trie.

---

## 5. Prefix and Suffix Search

**Đề bài chi tiết:**
Thiết kế class `WordFilter` nhận vào danh sách các từ. Implement hàm `f(String prefix, String suffix)` trả về index lớn nhất của một từ trong từ điển mà có tiền tố và hậu tố trùng khớp.

**Phân tích thuật toán:**
Ý tưởng hay: Với mỗi từ `apple`, ta lưu các dạng sau vào Trie: `#apple`, `e#apple`, `le#apple`, `ple#apple`, `pple#apple`, `apple#apple`. Sau đó, ta search theo định dạng `suffix + # + prefix`. Bằng cách này ta dùng Trie để giải quyết cả prefix và suffix cùng lúc. Mỗi node lưu giữ giá trị index lớn nhất của từ đi qua đó.

**Mã nguồn Java:**
```java
class WordFilter {
    class TrieNode {
        TrieNode[] children = new TrieNode[27]; // 26 letters + '#'
        int weight = -1;
    }
    
    TrieNode root;

    public WordFilter(String[] words) {
        root = new TrieNode();
        for (int weight = 0; weight < words.length; weight++) {
            String word = words[weight] + "{"; // '{' is char directly after 'z', map to 26
            for (int i = 0; i < word.length(); i++) {
                TrieNode node = root;
                node.weight = weight;
                // Add suffix part + '{' + prefix part
                for (int j = i; j < 2 * word.length() - 1; j++) {
                    int k = word.charAt(j % word.length()) - 'a';
                    if (node.children[k] == null) {
                        node.children[k] = new TrieNode();
                    }
                    node = node.children[k];
                    node.weight = weight;
                }
            }
        }
    }
    
    public int f(String prefix, String suffix) {
        TrieNode node = root;
        String search = suffix + "{" + prefix;
        for (char c : search.toCharArray()) {
            if (node.children[c - 'a'] == null) {
                return -1;
            }
            node = node.children[c - 'a'];
        }
        return node.weight;
    }
}
```

**Độ phức tạp:**
- Thời gian: O(N * K^2) để build Trie (với K là max length, tối đa 10). O(K) cho mỗi truy vấn f.
- Không gian: O(N * K^2)

---

## 6. Longest Word in Dictionary

**Đề bài chi tiết:**
Cho một mảng các chuỗi `words`, tìm chuỗi dài nhất trong mảng mà có thể được tạo ra bằng cách thêm từng chữ cái một vào cuối chuỗi từ một chuỗi bắt đầu có độ dài 1. Nếu có nhiều kết quả, trả về chuỗi có thứ tự từ điển nhỏ nhất.

**Phân tích thuật toán:**
Xây dựng Trie chứa tất cả các từ. Duyệt DFS hoặc BFS trên Trie bắt đầu từ root. Chỉ đi xuống các node có `isEndOfWord = true` (tức là prefix của nó là một từ hợp lệ có trong mảng). Giữ lại từ dài nhất và ưu tiên duyệt từ `a` đến `z` để đảm bảo thứ tự từ điển nhỏ nhất.

**Mã nguồn Java:**
```java
class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        String word = "";
    }
    
    public String longestWord(String[] words) {
        TrieNode root = new TrieNode();
        for (String w : words) {
            TrieNode node = root;
            for (char c : w.toCharArray()) {
                if (node.children[c - 'a'] == null) {
                    node.children[c - 'a'] = new TrieNode();
                }
                node = node.children[c - 'a'];
            }
            node.word = w;
        }
        
        String ans = "";
        dfs(root);
        return res;
    }
    
    String res = "";
    
    private void dfs(TrieNode node) {
        if (node == null) return;
        if (node.word.length() > res.length()) {
            res = node.word;
        }
        for (int i = 0; i < 26; i++) {
            if (node.children[i] != null && node.children[i].word.length() > 0) {
                dfs(node.children[i]);
            }
        }
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(\sum w_i)$ với $w_i$ là độ dài các từ để đưa vào Trie và cũng duyệt DFS.
- Không gian: $O(\sum w_i)$ cho Trie.

---

## 7. Maximum XOR of Two Numbers in an Array

**Đề bài chi tiết:**
Cho mảng các số nguyên không âm `nums`, tìm XOR lớn nhất giữa 2 số trong mảng.

**Phân tích thuật toán:**
Biểu diễn từng số trong dạng nhị phân 31-bit. Sử dụng Bitwise Trie (Trie có 2 children là 0 và 1). Duyệt từ bit cao (30) xuống bit thấp (0). Với mỗi số trong mảng, muốn tìm số có XOR cực đại với nó, ta đi xuống con đường có bit khác nhau (để XOR = 1).

**Mã nguồn Java:**
```java
class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[2];
    }
    
    public int findMaximumXOR(int[] nums) {
        TrieNode root = new TrieNode();
        for (int num : nums) {
            TrieNode node = root;
            for (int i = 30; i >= 0; i--) {
                int bit = (num >> i) & 1;
                if (node.children[bit] == null) {
                    node.children[bit] = new TrieNode();
                }
                node = node.children[bit];
            }
        }
        
        int max = 0;
        for (int num : nums) {
            TrieNode node = root;
            int currentXor = 0;
            for (int i = 30; i >= 0; i--) {
                int bit = (num >> i) & 1;
                int toggled = 1 - bit;
                if (node.children[toggled] != null) {
                    currentXor += (1 << i);
                    node = node.children[toggled];
                } else {
                    node = node.children[bit];
                }
            }
            max = Math.max(max, currentXor);
        }
        return max;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N)$ vì mỗi số tối đa 31 bit thao tác chèn và truy vấn mất hằng số $O(31)$.
- Không gian: $O(N)$ không gian lưu Trie cây nhị phân.

---

## 8. Palindrome Pairs

**Đề bài chi tiết:**
Cho một danh sách các từ duy nhất, tìm tất cả các cặp chỉ số `(i, j)` sao cho sự kết hợp `words[i] + words[j]` là một chuỗi Palindrome (chuỗi đối xứng).

**Phân tích thuật toán:**
Dùng Trie nhưng chèn vào Trie **chuỗi đảo ngược** của mỗi từ, lưu trữ cả id của từ đó vào các node kết thúc và cả vào danh sách id trên các node mà nếu từ điểm đó đến cuối từ là một palindrome. Duyệt từng từ xem tìm thấy đảo ngược của nó trên Trie không.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        int wordId = -1;
        List<Integer> palindromePrefixIds = new ArrayList<>();
    }
    
    private boolean isPalindrome(String word, int i, int j) {
        while (i < j) {
            if (word.charAt(i++) != word.charAt(j--)) return false;
        }
        return true;
    }
    
    public List<List<Integer>> palindromePairs(String[] words) {
        TrieNode root = new TrieNode();
        for (int i = 0; i < words.length; i++) {
            String word = words[i];
            TrieNode node = root;
            for (int j = word.length() - 1; j >= 0; j--) {
                if (isPalindrome(word, 0, j)) {
                    node.palindromePrefixIds.add(i);
                }
                char c = word.charAt(j);
                if (node.children[c - 'a'] == null) {
                    node.children[c - 'a'] = new TrieNode();
                }
                node = node.children[c - 'a'];
            }
            node.wordId = i;
            node.palindromePrefixIds.add(i);
        }
        
        List<List<Integer>> res = new ArrayList<>();
        for (int i = 0; i < words.length; i++) {
            String word = words[i];
            TrieNode node = root;
            boolean found = true;
            for (int j = 0; j < word.length(); j++) {
                if (node.wordId != -1 && node.wordId != i && isPalindrome(word, j, word.length() - 1)) {
                    res.add(Arrays.asList(i, node.wordId));
                }
                node = node.children[word.charAt(j) - 'a'];
                if (node == null) {
                    found = false;
                    break;
                }
            }
            if (found) {
                for (int id : node.palindromePrefixIds) {
                    if (id != i) {
                        res.add(Arrays.asList(i, id));
                    }
                }
            }
        }
        return res;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \times L^2)$ với $L$ là độ dài trung bình của các chuỗi.
- Không gian: $O(N \times L)$ cho cấu trúc Trie.

---

## 9. Map Sum Pairs

**Đề bài chi tiết:**
Triển khai class `MapSum` có các hàm `insert(key, val)` thêm chuỗi `key` với giá trị `val`. Nếu `key` tồn tại thì ghi đè `val`. Hàm `sum(prefix)` tính tổng của mọi giá trị có key bắt đầu bằng `prefix`.

**Phân tích thuật toán:**
Dùng cấu trúc Trie. Mỗi node lưu thêm trường `score`. Khi `insert`, ta cần cộng giá trị mới vào, đồng thời nếu `key` đã tồn tại trước đó, ta phải trừ đi giá trị cũ. Do đó ta dùng thêm một HashMap quản lý giá trị của từng key gốc. Khi tính sum theo prefix, ta chỉ cần đi tới node ứng với chữ cuối của prefix và lấy ra `score`.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;

class MapSum {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        int score = 0;
    }
    
    private TrieNode root;
    private Map<String, Integer> map;

    public MapSum() {
        root = new TrieNode();
        map = new HashMap<>();
    }
    
    public void insert(String key, int val) {
        int delta = val - map.getOrDefault(key, 0);
        map.put(key, val);
        TrieNode node = root;
        for (char c : key.toCharArray()) {
            if (node.children[c - 'a'] == null) {
                node.children[c - 'a'] = new TrieNode();
            }
            node = node.children[c - 'a'];
            node.score += delta;
        }
    }
    
    public int sum(String prefix) {
        TrieNode node = root;
        for (char c : prefix.toCharArray()) {
            if (node.children[c - 'a'] == null) return 0;
            node = node.children[c - 'a'];
        }
        return node.score;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(K)$ cho `insert` và $O(P)$ cho `sum` ($K$ độ dài của key, $P$ là độ dài của prefix).
- Không gian: $O(N)$ lưu trữ trong Trie và HashMap.

---

## 10. Camelcase Matching

**Đề bài chi tiết:**
Kiểm tra xem một mẫu (pattern) chứa cả chữ in hoa và in thường có phải là Camelcase pattern của một chuỗi nhất định không. Chuỗi pattern có thể khớp nếu ta chèn thêm vài kí tự chữ thường vào pattern.

**Phân tích thuật toán:**
Bài này có thể dùng Two Pointers hoặc Trie. Nếu có nhiều queries với một pattern (hoặc ngược lại), dùng Trie chứa pattern/words để tìm nhanh chóng. Thuật toán: duyệt từ qua Trie, cho phép bỏ qua ký tự thường (lower case) trên nhánh con, nhưng không được phép bỏ qua ký tự viết hoa.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[58]; // A-Z, a-z
        boolean isEnd;
    }
    // Simple direct approach is Two Pointers, but can use Trie for dictionary
    // Here we demonstrate the Two Pointer which is optimal, to adapt Trie logic:
    public List<Boolean> camelMatch(String[] queries, String pattern) {
        List<Boolean> res = new ArrayList<>();
        for (String q : queries) {
            res.add(isMatch(q, pattern));
        }
        return res;
    }
    
    private boolean isMatch(String query, String pattern) {
        int i = 0;
        for (char c : query.toCharArray()) {
            if (i < pattern.length() && c == pattern.charAt(i)) {
                i++;
            } else if (Character.isUpperCase(c)) {
                return false;
            }
        }
        return i == pattern.length();
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \times L)$ với $N$ là số queries, $L$ độ dài query.
- Không gian: $O(1)$. (Dùng Two pointers thay vì Trie cho bài này hiệu quả không gian tốt nhất).

---

## 11. Search Suggestions System

**Đề bài chi tiết:**
Cho một mảng các chuỗi `products` và một chuỗi `searchWord`. Thiết kế một hệ thống gợi ý từ khóa. Mỗi khi người dùng gõ một ký tự của `searchWord`, hệ thống sẽ trả về danh sách tối đa 3 sản phẩm có tiền tố khớp với chuỗi đã gõ. Các sản phẩm được trả về phải được sắp xếp theo thứ tự từ điển.

**Phân tích thuật toán:**
Xây dựng một Trie để lưu trữ các từ trong `products`. Để tối ưu hóa, tại mỗi node của Trie, ta có thể lưu một danh sách (PriorityQueue hoặc mảng đã sắp xếp) chứa tối đa 3 từ có thứ tự từ điển nhỏ nhất đi qua node đó. Khi duyệt qua từng ký tự của `searchWord`, ta chỉ cần đi xuống Trie và lấy danh sách đã lưu sẵn ở mỗi node.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        List<String> suggestions = new ArrayList<>();
    }
    
    public List<List<String>> suggestedProducts(String[] products, String searchWord) {
        Arrays.sort(products);
        TrieNode root = new TrieNode();
        for (String p : products) {
            TrieNode node = root;
            for (char c : p.toCharArray()) {
                if (node.children[c - 'a'] == null) {
                    node.children[c - 'a'] = new TrieNode();
                }
                node = node.children[c - 'a'];
                if (node.suggestions.size() < 3) {
                    node.suggestions.add(p);
                }
            }
        }
        
        List<List<String>> res = new ArrayList<>();
        TrieNode node = root;
        boolean notFound = false;
        
        for (char c : searchWord.toCharArray()) {
            if (notFound || node.children[c - 'a'] == null) {
                res.add(new ArrayList<>());
                notFound = true;
            } else {
                node = node.children[c - 'a'];
                res.add(node.suggestions);
            }
        }
        
        return res;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log N + N \times L + S)$ với $N$ là số từ, $L$ là độ dài tối đa của từ, $S$ là độ dài của `searchWord`. Việc sắp xếp `products` ban đầu tốn $O(N \log N \times L)$.
- Không gian: $O(N \times L)$ cho cấu trúc dữ liệu Trie và lưu trữ các suggestions.

---

## 12. Maximum XOR With an Element From Array

**Đề bài chi tiết:**
Cho mảng `nums` chứa các số nguyên không âm và mảng 2D `queries`, trong đó `queries[i] = [x_i, m_i]`. Với mỗi truy vấn, tìm giá trị lớn nhất của `x_i XOR nums[j]` sao cho `nums[j] <= m_i`. Nếu không có `nums[j]` nào thoả mãn, kết quả của truy vấn là -1.

**Phân tích thuật toán:**
Đây là phiên bản offline của bài toán Maximum XOR. Đầu tiên, sắp xếp mảng `nums` tăng dần. Đồng thời, lưu trữ chỉ số ban đầu của các truy vấn và sắp xếp chúng theo `m_i` tăng dần. Duyệt qua từng truy vấn, chèn các số `nums[j] <= m_i` vào Bitwise Trie. Sau đó, tìm Max XOR cho `x_i` trên Trie đã xây dựng. Nếu Trie rỗng, trả về -1.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[2];
    }
    
    TrieNode root = new TrieNode();
    
    private void insert(int num) {
        TrieNode node = root;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if (node.children[bit] == null) {
                node.children[bit] = new TrieNode();
            }
            node = node.children[bit];
        }
    }
    
    private int getMaxXor(int num) {
        if (root.children[0] == null && root.children[1] == null) return -1;
        TrieNode node = root;
        int max = 0;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            int toggled = 1 - bit;
            if (node.children[toggled] != null) {
                max |= (1 << i);
                node = node.children[toggled];
            } else {
                node = node.children[bit];
            }
        }
        return max;
    }
    
    public int[] maximizeXor(int[] nums, int[][] queries) {
        Arrays.sort(nums);
        int q = queries.length;
        int[][] sortedQueries = new int[q][3];
        for (int i = 0; i < q; i++) {
            sortedQueries[i][0] = queries[i][0]; // x_i
            sortedQueries[i][1] = queries[i][1]; // m_i
            sortedQueries[i][2] = i; // original index
        }
        
        Arrays.sort(sortedQueries, (a, b) -> Integer.compare(a[1], b[1]));
        int[] ans = new int[q];
        int j = 0;
        int n = nums.length;
        
        for (int[] query : sortedQueries) {
            int x = query[0];
            int m = query[1];
            int idx = query[2];
            
            while (j < n && nums[j] <= m) {
                insert(nums[j]);
                j++;
            }
            
            ans[idx] = getMaxXor(x);
        }
        
        return ans;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log N + Q \log Q + N \times 32 + Q \times 32)$ với $N$ là kích thước của `nums` và $Q$ là số truy vấn.
- Không gian: $O(N)$ lưu trữ Bitwise Trie.

---

## 13. Word Search

**Đề bài chi tiết:**
Cho một bảng lưới 2D `board` chứa các ký tự và một chuỗi `word`. Hãy kiểm tra xem chuỗi `word` có tồn tại trong lưới không. Từ có thể được tạo thành bằng cách nối các ô kề nhau (ngang hoặc dọc). Mỗi ô chỉ được dùng một lần cho một từ.

**Phân tích thuật toán:**
Mặc dù có thể giải bài này bằng cách chỉ dùng DFS (Backtracking) để duyệt lưới mà không cần Trie, việc xem chuỗi `word` là một Trie với 1 nhánh duy nhất (hoặc sử dụng Trie khi có nhiều `word`) giúp thuật toán cắt tỉa nhánh tốt hơn. Đối với 1 từ duy nhất, DFS kiểm tra từng ký tự tương ứng của `word` tại mỗi bước đệ quy.

**Mã nguồn Java:**
```java
class Solution {
    public boolean exist(char[][] board, String word) {
        int rows = board.length;
        int cols = board[0].length;
        
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (dfs(board, i, j, word, 0)) {
                    return true;
                }
            }
        }
        return false;
    }
    
    private boolean dfs(char[][] board, int i, int j, String word, int index) {
        if (index == word.length()) return true;
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length || board[i][j] != word.charAt(index)) {
            return false;
        }
        
        char temp = board[i][j];
        board[i][j] = '#'; // Mark as visited
        
        boolean found = dfs(board, i + 1, j, word, index + 1) ||
                        dfs(board, i - 1, j, word, index + 1) ||
                        dfs(board, i, j + 1, word, index + 1) ||
                        dfs(board, i, j - 1, word, index + 1);
                        
        board[i][j] = temp; // Backtrack
        return found;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(M \times N \times 3^L)$ với $M \times N$ là số lượng ô trong lưới, $L$ là độ dài của `word`. Ta nhân với 3 vì sau hướng đầu tiên, 3 hướng còn lại được tiếp tục.
- Không gian: $O(L)$ cho call stack của hàm đệ quy.

---

## 14. Vowel Spellchecker

**Đề bài chi tiết:**
Cho một danh sách các từ đúng chính tả `wordlist` và danh sách các truy vấn `queries`. Trả về mảng các từ tương ứng sau khi kiểm tra chính tả. Các quy tắc:
1. Nếu từ khớp hoàn toàn, trả về từ đó.
2. Nếu từ chỉ khác về in hoa/in thường, trả về từ đầu tiên xuất hiện trong `wordlist`.
3. Nếu từ khác về nguyên âm ('a', 'e', 'i', 'o', 'u'), trả về từ đầu tiên khớp trong `wordlist` (không phân biệt nguyên âm và in hoa/thường).
4. Nếu không khớp bất kỳ luật nào, trả về chuỗi rỗng.

**Phân tích thuật toán:**
Dùng các HashMap để lưu ba trạng thái của từ điển (Trie cũng có thể được dùng, nhưng HashMap cho hiệu năng trực tiếp hơn). 
- `words_perfect`: Lưu các từ nguyên gốc.
- `words_cap`: Lưu từ chuẩn hóa về chữ thường (lowercase) và map với từ xuất hiện đầu tiên.
- `words_vow`: Lưu từ chuẩn hóa chữ thường đồng thời thay thế các nguyên âm bằng ký tự '*', map với từ xuất hiện đầu tiên.
Với mỗi truy vấn, thực hiện tìm kiếm theo thứ tự ưu tiên của luật trên.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public String[] spellchecker(String[] wordlist, String[] queries) {
        Set<String> wordsPerfect = new HashSet<>(Arrays.asList(wordlist));
        Map<String, String> wordsCap = new HashMap<>();
        Map<String, String> wordsVow = new HashMap<>();
        
        for (String word : wordlist) {
            String lower = word.toLowerCase();
            wordsCap.putIfAbsent(lower, word);
            
            String devowel = replaceVowels(lower);
            wordsVow.putIfAbsent(devowel, word);
        }
        
        String[] ans = new String[queries.length];
        for (int i = 0; i < queries.length; i++) {
            ans[i] = solve(queries[i], wordsPerfect, wordsCap, wordsVow);
        }
        return ans;
    }
    
    private String solve(String query, Set<String> wordsPerfect, Map<String, String> wordsCap, Map<String, String> wordsVow) {
        if (wordsPerfect.contains(query)) return query;
        
        String lower = query.toLowerCase();
        if (wordsCap.containsKey(lower)) return wordsCap.get(lower);
        
        String devowel = replaceVowels(lower);
        if (wordsVow.containsKey(devowel)) return wordsVow.get(devowel);
        
        return "";
    }
    
    private String replaceVowels(String word) {
        StringBuilder sb = new StringBuilder();
        for (char c : word.toCharArray()) {
            if (isVowel(c)) sb.append('*');
            else sb.append(c);
        }
        return sb.toString();
    }
    
    private boolean isVowel(char c) {
        return c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u';
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \times L + Q \times L)$ với $N$ là số lượng từ trong `wordlist`, $Q$ là số lượng queries, $L$ là độ dài trung bình của các từ.
- Không gian: $O(N \times L)$ để lưu HashMaps và Sets.

---

## 15. Design Search Autocomplete System

**Đề bài chi tiết:**
Thiết kế một hệ thống autocomplete (tự động hoàn thành). Nhận vào các câu lịch sử và tần suất (số lần người dùng gõ). Khi người dùng gõ một ký tự mới, hệ thống cần gợi ý tối đa 3 câu lịch sử có tiền tố khớp với các ký tự đã gõ, sắp xếp giảm dần theo tần suất. Nếu có cùng tần suất, ưu tiên thứ tự từ điển (nhỏ nhất).

**Phân tích thuật toán:**
Ta lưu tất cả các câu vào Trie. Mỗi node trong Trie sẽ lưu lại một danh sách hoặc HashMap chứa thông tin về tất cả các câu đi qua (và tần suất của chúng). Khi người dùng gõ từng ký tự, ta đi sâu xuống node hiện tại của Trie. Ở node tương ứng, ta lấy tất cả câu thoả mãn, sau đó dùng `PriorityQueue` (Max-Heap) để trích xuất ra Top 3 câu phù hợp.

**Mã nguồn Java:**
```java
import java.util.*;

class AutocompleteSystem {
    class TrieNode {
        Map<Character, TrieNode> children = new HashMap<>();
        Map<String, Integer> counts = new HashMap<>();
    }
    
    TrieNode root;
    TrieNode curr;
    String prefix;

    public AutocompleteSystem(String[] sentences, int[] times) {
        root = new TrieNode();
        curr = root;
        prefix = "";
        for (int i = 0; i < sentences.length; i++) {
            addSentence(sentences[i], times[i]);
        }
    }
    
    private void addSentence(String sentence, int count) {
        TrieNode node = root;
        for (char c : sentence.toCharArray()) {
            node.children.putIfAbsent(c, new TrieNode());
            node = node.children.get(c);
            node.counts.put(sentence, node.counts.getOrDefault(sentence, 0) + count);
        }
    }
    
    public List<String> input(char c) {
        if (c == '#') {
            addSentence(prefix, 1);
            prefix = "";
            curr = root;
            return new ArrayList<>();
        }
        
        prefix += c;
        if (curr != null) {
            curr = curr.children.get(c);
        }
        
        if (curr == null) return new ArrayList<>();
        
        PriorityQueue<Map.Entry<String, Integer>> pq = new PriorityQueue<>(
            (a, b) -> a.getValue().equals(b.getValue()) ? 
                      a.getKey().compareTo(b.getKey()) : 
                      b.getValue() - a.getValue()
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

**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N \times L)$. Mỗi lần `input()` chạy vào node mất $O(1)$ và lọc top 3 tốn $O(K \log K)$ với $K$ là số lượng câu cùng tiền tố tại node đó.
- Không gian: $O(N \times L)$ cho Trie và HashMap.

---

## 16. Multi-Search

**Đề bài chi tiết:**
Cho một chuỗi văn bản $T$ và một mảng các chuỗi nhỏ `smalls`, hãy tìm tất cả các vị trí xuất hiện của mỗi chuỗi `smalls` trong văn bản $T$.

**Phân tích thuật toán:**
Để giải hiệu quả, có thể xây dựng một Trie chứa tất cả các chuỗi trong `smalls`. Sau đó, ta có thể duyệt qua từng vị trí `i` trong chuỗi $T$. Tại mỗi vị trí `i`, ta coi nó như điểm bắt đầu của một chuỗi, và đi duyệt trong Trie. Nếu gặp node có đánh dấu kết thúc (`isEndOfWord = true`), ta lưu lại vị trí `i` vào kết quả cho chuỗi nhỏ tương ứng. Cấu trúc Aho-Corasick cũng là giải pháp xuất sắc nhưng Trie cơ bản là đủ để pass trong hầu hết các trường hợp.

**Mã nguồn Java:**
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
            TrieNode node = root;
            for (char c : smalls[i].toCharArray()) {
                if (node.children[c - 'a'] == null) {
                    node.children[c - 'a'] = new TrieNode();
                }
                node = node.children[c - 'a'];
            }
            node.wordIndex = i;
        }
        
        List<Integer>[] resList = new ArrayList[smalls.length];
        for (int i = 0; i < smalls.length; i++) {
            resList[i] = new ArrayList<>();
        }
        
        for (int i = 0; i < big.length(); i++) {
            TrieNode node = root;
            for (int j = i; j < big.length(); j++) {
                char c = big.charAt(j);
                if (node.children[c - 'a'] == null) break;
                node = node.children[c - 'a'];
                if (node.wordIndex != -1) {
                    resList[node.wordIndex].add(i);
                }
            }
        }
        
        int[][] result = new int[smalls.length][];
        for (int i = 0; i < smalls.length; i++) {
            result[i] = new int[resList[i].size()];
            for (int j = 0; j < resList[i].size(); j++) {
                result[i][j] = resList[i].get(j);
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(K + M^2)$ (hoặc $O(K + M \times L)$) với $K$ là tổng độ dài `smalls`, $M$ là chiều dài của `big`, $L$ là chiều dài lớn nhất của một từ trong `smalls`.
- Không gian: $O(K)$ cho kích thước của Trie.

---

## 17. Stream of Characters

**Đề bài chi tiết:**
Thiết kế cấu trúc dữ liệu nhận một dòng chảy ký tự (Stream of Characters). Class `StreamChecker` khởi tạo với một danh sách các từ. Hàm `query(char letter)` nhận ký tự tiếp theo và trả về `true` nếu bất kỳ hậu tố nào của các ký tự đã nhận cho đến nay khớp với một từ trong mảng từ ban đầu.

**Phân tích thuật toán:**
Xây dựng một Trie lưu trữ các từ trong danh sách nhưng **đảo ngược** từ (Lưu từ cuối lên đầu). Giữ một `StringBuilder` lưu lịch sử các ký tự đã query. Khi `query()` được gọi, thêm ký tự mới vào cuối, sau đó duyệt ngược từ cuối của `StringBuilder` lên đầu và tìm trong Trie. Nếu tới node đánh dấu `isEndOfWord`, trả về `true`.

**Mã nguồn Java:**
```java
class StreamChecker {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean isWord;
    }
    
    TrieNode root = new TrieNode();
    StringBuilder sb = new StringBuilder();

    public StreamChecker(String[] words) {
        for (String w : words) {
            TrieNode node = root;
            for (int i = w.length() - 1; i >= 0; i--) {
                char c = w.charAt(i);
                if (node.children[c - 'a'] == null) {
                    node.children[c - 'a'] = new TrieNode();
                }
                node = node.children[c - 'a'];
            }
            node.isWord = true;
        }
    }
    
    public boolean query(char letter) {
        sb.append(letter);
        TrieNode node = root;
        for (int i = sb.length() - 1; i >= 0; i--) {
            char c = sb.charAt(i);
            if (node.children[c - 'a'] == null) {
                return false;
            }
            node = node.children[c - 'a'];
            if (node.isWord) return true;
        }
        return false;
    }
}
```

**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N \times L)$ với $N$ là số từ, $L$ là độ dài cực đại. Mỗi truy vấn mất $O(W)$ với $W$ là chiều dài tối đa của từ (do ta giới hạn duyệt ngược độ dài bằng độ sâu lớn nhất của Trie).
- Không gian: $O(N \times L)$ cho cấu trúc Trie.

---

## 18. Index Pairs of a String

**Đề bài chi tiết:**
Cho một chuỗi văn bản `text` và danh sách các chuỗi `words`. Tìm tất cả các cặp chỉ số `[i, j]` sao cho `text[i..j]` khớp với một chuỗi nằm trong `words`. Các cặp chỉ số cần trả về phải sắp xếp theo `i` tăng dần, nếu `i` bằng nhau thì `j` tăng dần.

**Phân tích thuật toán:**
Tương tự bài Multi-Search. Xây dựng Trie chứa các chuỗi trong `words`. Duyệt từ trái qua phải qua từng phần tử của chuỗi `text` (làm điểm xuất phát `i`). Tại mỗi điểm, đi sâu dần vào Trie (tăng `j`) và lưu lại bất cứ điểm nào mà `isEndOfWord` là `true`.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean isWord = false;
    }
    
    public int[][] indexPairs(String text, String[] words) {
        TrieNode root = new TrieNode();
        for (String w : words) {
            TrieNode node = root;
            for (char c : w.toCharArray()) {
                if (node.children[c - 'a'] == null) {
                    node.children[c - 'a'] = new TrieNode();
                }
                node = node.children[c - 'a'];
            }
            node.isWord = true;
        }
        
        List<int[]> res = new ArrayList<>();
        for (int i = 0; i < text.length(); i++) {
            TrieNode node = root;
            for (int j = i; j < text.length(); j++) {
                char c = text.charAt(j);
                if (node.children[c - 'a'] == null) break;
                node = node.children[c - 'a'];
                if (node.isWord) {
                    res.add(new int[]{i, j});
                }
            }
        }
        
        int[][] ans = new int[res.size()][2];
        for (int i = 0; i < res.size(); i++) {
            ans[i] = res.get(i);
        }
        return ans;
    }
}
```

**Độ phức tạp:**
- Thời gian: Khởi tạo Trie $O(\sum |words|)$. Tìm kiếm tốn $O(M^2)$ với $M$ là chiều dài của `text`.
- Không gian: $O(\sum |words|)$ bộ nhớ cho Trie.

---

## 19. Concatenated Words

**Đề bài chi tiết:**
Cho một mảng các chuỗi `words` (không trùng lặp). Trả về tất cả các từ trong mảng có thể được tạo thành bằng cách nối ít nhất hai từ ngắn hơn (cũng nằm trong `words`).

**Phân tích thuật toán:**
Có thể sử dụng Trie kết hợp với DFS (có Memoization). Trước tiên, đưa tất cả các từ vào Trie. Với mỗi từ, dùng đệ quy DFS để kiểm tra xem nó có thể được cắt thành các mảnh nhỏ nằm trong Trie hay không. Lưu ý không được cắt chính nó làm mảnh duy nhất (yêu cầu ít nhất 2 từ). Hoặc có thể duyệt theo thứ tự từ ngắn tới dài (sắp xếp), chỉ đưa các từ ngắn vào Trie trước khi kiểm tra các từ dài.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public List<String> findAllConcatenatedWordsInADict(String[] words) {
        Arrays.sort(words, (a, b) -> a.length() - b.length());
        List<String> res = new ArrayList<>();
        Set<String> preWords = new HashSet<>();
        
        for (String word : words) {
            if (word.isEmpty()) continue;
            if (canForm(word, preWords)) {
                res.add(word);
            }
            preWords.add(word);
        }
        return res;
    }
    
    private boolean canForm(String word, Set<String> dict) {
        if (dict.isEmpty()) return false;
        boolean[] dp = new boolean[word.length() + 1];
        dp[0] = true;
        
        for (int i = 1; i <= word.length(); i++) {
            for (int j = 0; j < i; j++) {
                if (!dp[j]) continue;
                if (dict.contains(word.substring(j, i))) {
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[word.length()];
    }
}
```
*(Ghi chú: Bài này dùng HashSet và DP (Quy hoạch động) thường dễ implement và nhanh hơn xây Trie thuần túy, nhưng cơ chế bản chất là Prefix Search).*

**Độ phức tạp:**
- Thời gian: $O(N \log N + N \times L^3)$ do cắt `substring` tốn $O(L)$, với $N$ là số từ, $L$ là độ dài lớn nhất.
- Không gian: $O(N \times L)$ lưu trữ Hash Set.

---

## 20. Subarrays with K Different Integers

**Đề bài chi tiết:**
Cho mảng số nguyên `nums` và một số nguyên `k`. Trả về số lượng mảng con liên tiếp (subarrays) chứa đúng `k` số nguyên khác nhau.

**Phân tích thuật toán:**
Mặc dù vấn đề liên quan đến việc tìm kiếm các mẫu mảng con, bài toán này hiệu quả nhất khi sử dụng kĩ thuật **Sliding Window (Cửa sổ trượt)** chứ không phải Trie (do kích thước của bảng chữ cái số nguyên rộng và Trie cần $O(N^2)$). Cụ thể, số lượng mảng con có chính xác `K` phần tử phân biệt bằng số lượng mảng con có **tối đa** `K` phần tử phân biệt trừ đi số lượng mảng con có **tối đa** `K - 1` phần tử phân biệt. Hàm phụ `atMostK(nums, k)` sử dụng 2 pointers.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int subarraysWithKDistinct(int[] nums, int k) {
        return atMostK(nums, k) - atMostK(nums, k - 1);
    }
    
    private int atMostK(int[] nums, int k) {
        int count = 0;
        int left = 0;
        Map<Integer, Integer> freq = new HashMap<>();
        
        for (int right = 0; right < nums.length; right++) {
            if (freq.getOrDefault(nums[right], 0) == 0) k--;
            freq.put(nums[right], freq.getOrDefault(nums[right], 0) + 1);
            
            while (k < 0) {
                freq.put(nums[left], freq.get(nums[left]) - 1);
                if (freq.get(nums[left]) == 0) k++;
                left++;
            }
            count += right - left + 1;
        }
        return count;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N)$ vì mỗi phần tử được duyệt (thêm và bớt) bằng 2 con trỏ nhiều nhất 2 lần.
- Không gian: $O(N)$ không gian sử dụng bởi HashMap lưu tần suất.

---

## 21. Lexicographical Numbers

**Đề bài chi tiết:**
Cho một số nguyên `n`, trả về danh sách các số nguyên từ 1 đến `n` theo thứ tự từ điển (lexicographical order).

**Phân tích thuật toán:**
Nếu coi các số từ 1 đến `n` như là các chuỗi, bài toán tương đương với việc chèn các số vào một Trie và thực hiện duyệt Pre-order (tiền thứ tự). Thay vì thực sự xây dựng Trie (sẽ tốn bộ nhớ $O(N)$), ta có thể mô phỏng quá trình duyệt này. Bắt đầu từ 1, nếu có thể nhân 10 (đi xuống con trái nhất trong Trie ảo) mà không vượt quá `n`, ta tiến hành. Nếu không, ta thử tăng thêm 1 (đi sang con kế tiếp bên phải). Nếu đạt `n` hoặc chữ số tận cùng là 9, ta chia cho 10 (quay lui lên node cha).

**Mã nguồn Java:**
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
                if (curr >= n) {
                    curr /= 10;
                }
                curr += 1;
                while (curr % 10 == 0) {
                    curr /= 10;
                }
            }
        }
        return res;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N)$, mỗi số được duyệt và thêm vào danh sách kết quả đúng 1 lần.
- Không gian: $O(1)$ không gian bổ sung (không tính danh sách kết quả `res`), do chỉ mô phỏng duyệt Trie bằng tính toán số học.

---

## 22. Delete Duplicate Folders in System

**Đề bài chi tiết:**
Cho danh sách các đường dẫn `paths` biểu diễn một hệ thống cây thư mục. Nếu có hai thư mục (hoặc nhiều hơn) có cấu trúc con giống hệt nhau, hãy xóa tất cả các thư mục đó cùng với các thư mục con của chúng. Trả về cấu trúc cây thư mục còn lại dưới dạng danh sách đường dẫn.

**Phân tích thuật toán:**
Xây dựng một cây đa phân (Trie) mô phỏng cấu trúc File System. Để phát hiện cấu trúc con trùng lặp, dùng Serialization (mã hóa cây con thành một chuỗi đại diện) ở trạng thái Post-order (duyệt hậu thứ tự) kết hợp Hashing. Nếu một chuỗi đại diện cấu trúc con xuất hiện nhiều hơn 1 lần, đánh dấu node đó là bị xóa. Cuối cùng, duyệt lại từ Root để thu thập các đường dẫn hợp lệ, bỏ qua các node bị đánh dấu.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    class FolderNode {
        String name;
        Map<String, FolderNode> children = new TreeMap<>();
        boolean isDeleted = false;
        
        FolderNode(String name) { this.name = name; }
    }
    
    Map<String, List<FolderNode>> serialMap = new HashMap<>();
    
    public List<List<String>> deleteDuplicateFolder(List<List<String>> paths) {
        FolderNode root = new FolderNode("");
        for (List<String> path : paths) {
            FolderNode node = root;
            for (String folder : path) {
                node.children.putIfAbsent(folder, new FolderNode(folder));
                node = node.children.get(folder);
            }
        }
        
        serialize(root);
        
        for (List<FolderNode> nodes : serialMap.values()) {
            if (nodes.size() > 1) {
                for (FolderNode node : nodes) {
                    node.isDeleted = true;
                }
            }
        }
        
        List<List<String>> res = new ArrayList<>();
        for (FolderNode child : root.children.values()) {
            collectPaths(child, new ArrayList<>(), res);
        }
        
        return res;
    }
    
    private String serialize(FolderNode node) {
        if (node.children.isEmpty()) return "";
        StringBuilder sb = new StringBuilder();
        
        for (FolderNode child : node.children.values()) {
            sb.append("(").append(child.name).append(serialize(child)).append(")");
        }
        
        String serial = sb.toString();
        serialMap.putIfAbsent(serial, new ArrayList<>());
        serialMap.get(serial).add(node);
        
        return serial;
    }
    
    private void collectPaths(FolderNode node, List<String> currentPath, List<List<String>> res) {
        if (node.isDeleted) return;
        
        currentPath.add(node.name);
        res.add(new ArrayList<>(currentPath));
        
        for (FolderNode child : node.children.values()) {
            collectPaths(child, currentPath, res);
        }
        
        currentPath.remove(currentPath.size() - 1);
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(V \log V \times L)$ với $V$ là số lượng node và $L$ là độ dài cực đại của chuỗi serialize.
- Không gian: $O(V \times L)$ bộ nhớ cho Trie và HashMap.

---

## 23. Find the Number of Common Prefixes

**Đề bài chi tiết:**
Cho hai mảng chuỗi `A` và `B`. Trả về một mảng chứa số lượng chuỗi trong mảng `A` có chung tiền tố với các chuỗi tương ứng trong mảng `B`. Cụ thể hơn (đây là một dạng điển hình): cho một chuỗi $S$, đếm số lượng chuỗi trong dictionary có tiền tố là $S$. 

**Phân tích thuật toán:**
Dùng một biến `count` lưu tại mỗi node của Trie. Ban đầu xây dựng Trie từ danh sách chuỗi, với mỗi ký tự đi qua, ta tăng `count` của node đó lên 1. Khi truy vấn tiền tố $P$, ta đi xuống theo các ký tự của $P$ trên Trie. Khi đi hết $P$, giá trị `count` tại node hiện tại chính là số lượng chuỗi có chung tiền tố đó.

**Mã nguồn Java:**
```java
import java.util.*;

class PrefixCounter {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        int count = 0;
    }
    
    TrieNode root;

    public PrefixCounter() {
        root = new TrieNode();
    }
    
    public void insert(String word) {
        TrieNode node = root;
        for (char c : word.toCharArray()) {
            if (node.children[c - 'a'] == null) {
                node.children[c - 'a'] = new TrieNode();
            }
            node = node.children[c - 'a'];
            node.count++;
        }
    }
    
    public int countPrefixes(String prefix) {
        TrieNode node = root;
        for (char c : prefix.toCharArray()) {
            if (node.children[c - 'a'] == null) {
                return 0;
            }
            node = node.children[c - 'a'];
        }
        return node.count;
    }
}
```

**Độ phức tạp:**
- Thời gian: Xây dựng Trie mất $O(N \times L)$, truy vấn mất $O(P)$ với $P$ là độ dài tiền tố.
- Không gian: $O(N \times L)$ cho kích thước Trie.

---

## 24. Group Shifted Strings

**Đề bài chi tiết:**
Cho một mảng các chuỗi, nhóm các chuỗi có cùng chu kỳ dịch chuyển lại với nhau. Ví dụ: "abc" và "bcd" thuộc cùng nhóm vì "abc" dịch 1 ký tự sẽ thành "bcd" ('a'->'b', 'b'->'c', 'c'->'d').

**Phân tích thuật toán:**
Hai chuỗi thuộc cùng một nhóm khi và chỉ khi khoảng cách tương đối giữa các ký tự liên tiếp của chúng là như nhau. Do đó, ta tính toán một chuỗi "chuẩn" (pattern) dựa trên khác biệt khoảng cách này làm Hash key (hoặc sử dụng Trie để phân loại). Phương pháp phổ biến và tối ưu hơn là dùng HashMap với key là dạng chuẩn của chuỗi.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public List<List<String>> groupStrings(String[] strings) {
        Map<String, List<String>> map = new HashMap<>();
        
        for (String s : strings) {
            String key = getShiftedKey(s);
            map.putIfAbsent(key, new ArrayList<>());
            map.get(key).add(s);
        }
        
        return new ArrayList<>(map.values());
    }
    
    private String getShiftedKey(String s) {
        if (s.length() == 1) return "0";
        StringBuilder sb = new StringBuilder();
        
        for (int i = 1; i < s.length(); i++) {
            int diff = s.charAt(i) - s.charAt(i - 1);
            if (diff < 0) diff += 26;
            sb.append(diff).append(",");
        }
        return sb.toString();
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \times L)$ với $N$ là số lượng chuỗi và $L$ là độ dài trung bình của chuỗi.
- Không gian: $O(N \times L)$ lưu trữ trong bộ nhớ HashMap.

---

## 25. LZW Compression Algorithm

**Đề bài chi tiết:**
Mô phỏng thuật toán nén LZW. Nhận vào một chuỗi ký tự, trả về danh sách các mã nguyên tương ứng với quá trình nén của thuật toán Lempel-Ziv-Welch (LZW).

**Phân tích thuật toán:**
LZW bắt đầu với một từ điển (dictionary) chứa tất cả các ký tự đơn lẻ. Nó sử dụng cơ chế Trie (hoặc HashMap thay thế Trie để dễ code) để tìm kiếm tiền tố dài nhất của chuỗi dữ liệu chưa xử lý. Nếu một chuỗi con chưa có trong từ điển, nó sẽ thêm chuỗi đó vào từ điển với một code mới, và xuất ra code của phần tiền tố đã có. Bản chất LZW xây dựng Trie một cách động (dynamic Trie) từ luồng ký tự đầu vào.

**Mã nguồn Java:**
```java
import java.util.*;

class LZW {
    public List<Integer> compress(String uncompressed) {
        int dictSize = 256;
        Map<String, Integer> dictionary = new HashMap<>();
        for (int i = 0; i < 256; i++) {
            dictionary.put(String.valueOf((char) i), i);
        }
        
        String w = "";
        List<Integer> result = new ArrayList<>();
        
        for (char c : uncompressed.toCharArray()) {
            String wc = w + c;
            if (dictionary.containsKey(wc)) {
                w = wc;
            } else {
                result.add(dictionary.get(w));
                dictionary.put(wc, dictSize++);
                w = String.valueOf(c);
            }
        }
        
        if (!w.equals("")) {
            result.add(dictionary.get(w));
        }
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N)$, thuật toán tiến lên dọc theo chuỗi. (Bản chất dùng HashMap ở đây tốn thêm thời gian hashing chuỗi, Trie sẽ triệt để $O(N)$).
- Không gian: $O(N)$ lưu trữ từ điển mới được sinh ra trong quá trình nén.

---

## 26. Encrypt and Decrypt Strings

**Đề bài chi tiết:**
Thiết kế bộ mã hóa `Encrypter`. Bộ khởi tạo nhận mảng `keys`, mảng chuỗi `values`, và `dictionary`. `encrypt(word)` mã hóa từng ký tự của `word` thành chuỗi 2 ký tự trong `values` tương ứng với `keys`. `decrypt(word)` nhận chuỗi đã mã hóa và đếm xem có bao nhiêu chuỗi hợp lệ trong `dictionary` nếu giải mã.

**Phân tích thuật toán:**
Cơ chế mã hóa là 1-nhiều nhưng vì ta chỉ cần đếm số lượng từ hợp lệ (thuộc `dictionary`) nên thay vì sinh ra mọi hoán vị giải mã, ta hãy mã hóa trước toàn bộ `dictionary`! Sau khi mã hóa các từ trong `dictionary`, ta đếm tần suất xuất hiện của chúng. Khi gọi hàm `decrypt(word)`, ta chỉ cần trả về tần suất của `word` vừa nhận vào.

**Mã nguồn Java:**
```java
import java.util.*;

class Encrypter {
    Map<Character, String> encMap;
    Map<String, Integer> decCount;

    public Encrypter(char[] keys, String[] values, String[] dictionary) {
        encMap = new HashMap<>();
        decCount = new HashMap<>();
        
        for (int i = 0; i < keys.length; i++) {
            encMap.put(keys[i], values[i]);
        }
        
        for (String word : dictionary) {
            String encrypted = encrypt(word);
            if (encrypted != null) {
                decCount.put(encrypted, decCount.getOrDefault(encrypted, 0) + 1);
            }
        }
    }
    
    public String encrypt(String word1) {
        StringBuilder sb = new StringBuilder();
        for (char c : word1.toCharArray()) {
            if (!encMap.containsKey(c)) return null;
            sb.append(encMap.get(c));
        }
        return sb.toString();
    }
    
    public int decrypt(String word2) {
        return decCount.getOrDefault(word2, 0);
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \times L)$ để khởi tạo, hàm `encrypt` là $O(L)$, hàm `decrypt` chỉ mất $O(L)$ thời gian hashing chuỗi.
- Không gian: $O(N \times L)$ lưu HashMap `decCount`.

---

## 27. Number of Matching Subsequences

**Đề bài chi tiết:**
Cho một chuỗi `s` và mảng chuỗi `words`. Trả về số lượng `words[i]` là chuỗi con (subsequence) của `s`. (Không cần liên tục).

**Phân tích thuật toán:**
Dùng mảng 26 List hoặc Queue để lưu trữ các con trỏ trỏ tới từng chuỗi trong `words` dựa trên ký tự đầu tiên đang đợi khớp. Duyệt qua từng ký tự `c` trong chuỗi `s`. Ta lấy tất cả các chuỗi đang chờ ký tự `c` và xóa ký tự đầu tiên của chúng (thực tế là tiến con trỏ lên), rồi đưa chúng vào List của ký tự cần thiết tiếp theo. Cấu trúc duyệt nhiều chuỗi cùng lúc dựa trên con trỏ này tương đồng với các trạng thái khi duyệt Trie.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    class Node {
        String word;
        int index;
        Node(String w, int i) {
            word = w;
            index = i;
        }
    }
    
    public int numMatchingSubseq(String s, String[] words) {
        List<Node>[] waiting = new ArrayList[26];
        for (int i = 0; i < 26; i++) waiting[i] = new ArrayList<>();
        
        for (String w : words) {
            waiting[w.charAt(0) - 'a'].add(new Node(w, 0));
        }
        
        int count = 0;
        for (char c : s.toCharArray()) {
            List<Node> currentWaiting = waiting[c - 'a'];
            waiting[c - 'a'] = new ArrayList<>();
            
            for (Node node : currentWaiting) {
                node.index++;
                if (node.index == node.word.length()) {
                    count++;
                } else {
                    waiting[node.word.charAt(node.index) - 'a'].add(node);
                }
            }
        }
        return count;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(|s| + \sum |words_i|)$ mỗi ký tự của mỗi chuỗi chỉ được xử lý một lần.
- Không gian: $O(W)$ với $W$ là số lượng chuỗi `words`.

---

## 28. Boggle Game Solver

**Đề bài chi tiết:**
Trò chơi Boggle chơi trên bảng $N \times M$ chứa các ký tự. Tìm tất cả các từ trong từ điển có thể tạo ra từ bảng Boggle (chữ cái nối tiếp nhau theo 8 hướng và không lặp lại ô).

**Phân tích thuật toán:**
Tương tự Word Search II nhưng ở đây cho phép di chuyển cả đường chéo (8 hướng). Xây dựng một Trie cho từ điển. Áp dụng DFS / Backtracking cho từng ô trên bảng. Trie giúp cắt tỉa hiệu quả: khi một chuỗi hiện tại không phải là tiền tố của bất kỳ từ nào, ta bỏ qua hoàn toàn.

**Mã nguồn Java:**
```java
import java.util.*;

class BoggleSolver {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        String word;
    }
    
    TrieNode root;
    int[] dx = {-1, -1, -1, 0, 0, 1, 1, 1};
    int[] dy = {-1, 0, 1, -1, 1, -1, 0, 1};
    
    public BoggleSolver(String[] dictionary) {
        root = new TrieNode();
        for (String w : dictionary) {
            TrieNode node = root;
            for (char c : w.toCharArray()) {
                if (node.children[c - 'A'] == null) node.children[c - 'A'] = new TrieNode();
                node = node.children[c - 'A'];
            }
            node.word = w;
        }
    }
    
    public List<String> getAllValidWords(char[][] board) {
        Set<String> res = new HashSet<>();
        int r = board.length, c = board[0].length;
        boolean[][] visited = new boolean[r][c];
        
        for (int i = 0; i < r; i++) {
            for (int j = 0; j < c; j++) {
                dfs(board, i, j, root, visited, res);
            }
        }
        return new ArrayList<>(res);
    }
    
    private void dfs(char[][] board, int i, int j, TrieNode p, boolean[][] visited, Set<String> res) {
        char ch = board[i][j];
        if (p.children[ch - 'A'] == null) return;
        p = p.children[ch - 'A'];
        
        if (p.word != null) {
            res.add(p.word);
        }
        
        visited[i][j] = true;
        for (int d = 0; d < 8; d++) {
            int ni = i + dx[d], nj = j + dy[d];
            if (ni >= 0 && ni < board.length && nj >= 0 && nj < board[0].length && !visited[ni][nj]) {
                dfs(board, ni, nj, p, visited, res);
            }
        }
        visited[i][j] = false;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(V \times 8^L)$ với $V$ số ô của bảng và $L$ chiều dài tối đa từ.
- Không gian: $O(\text{dictionary size})$ cho Trie và đệ quy call stack.

---

## 29. Routing Path Matching

**Đề bài chi tiết:**
Xây dựng một hệ thống Router. Có các luật dạng đường dẫn URL như `/user/:id/profile` (trong đó `:id` là biến). Với một đường dẫn cung cấp (VD: `/user/123/profile`), kiểm tra xem nó có khớp với mẫu nào hay không. Nếu có, bóc tách giá trị của tham số `:id`.

**Phân tích thuật toán:**
Sử dụng Radix Trie. Thay vì mỗi node lưu một ký tự, mỗi node sẽ lưu một phần (segment) của URL nằm giữa hai dấu `/`. Nếu segment bắt đầu bằng `:`, node đó được đánh dấu là param node, nó sẽ khớp với bất kỳ segment đầu vào nào ở vị trí tương ứng.

**Mã nguồn Java:**
```java
import java.util.*;

class Router {
    class RouteNode {
        Map<String, RouteNode> children = new HashMap<>();
        String paramName = null; // null nếu không phải param
        String routeHandler = null;
    }
    
    RouteNode root = new RouteNode();
    
    public void addRoute(String path, String handler) {
        String[] parts = path.split("/");
        RouteNode node = root;
        for (String part : parts) {
            if (part.isEmpty()) continue;
            
            if (part.startsWith(":")) {
                if (!node.children.containsKey(":param")) {
                    RouteNode pNode = new RouteNode();
                    pNode.paramName = part.substring(1);
                    node.children.put(":param", pNode);
                }
                node = node.children.get(":param");
            } else {
                node.children.putIfAbsent(part, new RouteNode());
                node = node.children.get(part);
            }
        }
        node.routeHandler = handler;
    }
    
    public String route(String path) {
        String[] parts = path.split("/");
        RouteNode node = root;
        for (String part : parts) {
            if (part.isEmpty()) continue;
            
            if (node.children.containsKey(part)) {
                node = node.children.get(part);
            } else if (node.children.containsKey(":param")) {
                node = node.children.get(":param");
                // Bóc tách parameter có thể lưu vào context ở đây
            } else {
                return "404 Not Found";
            }
        }
        return node.routeHandler != null ? node.routeHandler : "404 Not Found";
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(K)$ để thêm và tra cứu, trong đó $K$ là số lượng segment trong đường dẫn.
- Không gian: $O(N)$ lưu trữ các mẫu (pattern paths) vào Trie.

---

## 30. Word Squares

**Đề bài chi tiết:**
Cho danh sách các từ, tìm tất cả các "Word Squares" có thể được tạo ra. Một cấu trúc Word Square $k \times k$ là một ma trận trong đó hàng thứ `i` và cột thứ `i` đều là các chuỗi giống hệt nhau (VD: chuỗi dòng 0 = chuỗi cột 0, v.v...). Các từ được phép lặp lại.

**Phân tích thuật toán:**
Để tạo Word Square, khi ta đã có `k` từ, ở dòng `k` tiếp theo, từ đó phải có tiền tố cấu thành bởi các ký tự ở vị trí `k` của `k` từ bên trên. Do đó, việc tìm các từ có cùng tiền tố nhanh chóng là yếu tố quyết định. Xây dựng Trie chứa mọi từ. Sau đó dùng Backtracking, tại mỗi bước, lấy prefix tạo từ các chữ cái ghép chéo, tra trong Trie danh sách các từ khớp prefix và đi tiếp đệ quy.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        List<String> startWith = new ArrayList<>();
    }
    
    TrieNode root;
    
    private void buildTrie(String[] words) {
        root = new TrieNode();
        for (String w : words) {
            TrieNode node = root;
            for (char c : w.toCharArray()) {
                if (node.children[c - 'a'] == null) {
                    node.children[c - 'a'] = new TrieNode();
                }
                node = node.children[c - 'a'];
                node.startWith.add(w); // Lưu từ đi qua
            }
        }
    }
    
    private List<String> getWordsWithPrefix(String prefix) {
        TrieNode node = root;
        for (char c : prefix.toCharArray()) {
            if (node.children[c - 'a'] == null) return new ArrayList<>();
            node = node.children[c - 'a'];
        }
        return node.startWith;
    }
    
    public List<List<String>> wordSquares(String[] words) {
        buildTrie(words);
        List<List<String>> res = new ArrayList<>();
        int len = words[0].length();
        
        for (String w : words) {
            List<String> square = new ArrayList<>();
            square.add(w);
            backtrack(1, square, res, len);
        }
        return res;
    }
    
    private void backtrack(int step, List<String> square, List<List<String>> res, int len) {
        if (step == len) {
            res.add(new ArrayList<>(square));
            return;
        }
        
        StringBuilder prefixBuilder = new StringBuilder();
        for (String w : square) {
            prefixBuilder.append(w.charAt(step));
        }
        String prefix = prefixBuilder.toString();
        
        for (String candidate : getWordsWithPrefix(prefix)) {
            square.add(candidate);
            backtrack(step + 1, square, res, len);
            square.remove(square.size() - 1);
        }
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \times 26^L)$ trong trường hợp xấu nhất (Backtracking đầy đủ), với $L$ là độ dài từ.
- Không gian: $O(N \times L)$ không gian cho Trie và mảng kết quả.
