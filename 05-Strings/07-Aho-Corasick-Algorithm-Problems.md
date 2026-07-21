# 07 - Aho-Corasick Algorithm Problems

Tài liệu này cung cấp 30 bài tập về thuật toán Aho-Corasick và các biến thể của nó như Trie, Dictionary lookups. 10 bài đầu tiên sẽ có giải pháp chi tiết bằng Java 21.

## 1. Multi-Pattern Search
**Đề bài chi tiết:** Cho một văn bản `text` và danh sách các từ `words`. Hãy tìm tất cả các vị trí xuất hiện của mỗi từ trong văn bản. Trả về một Map chứa từ khóa làm key và danh sách các vị trí bắt đầu làm value.
**Phân tích thuật toán:** Sử dụng thuật toán Aho-Corasick chuẩn. Xây dựng Trie, sau đó thêm các liên kết thất bại (failure links) và liên kết từ điển (dictionary links). Cuối cùng, cho text chạy qua máy trạng thái (Automaton) để thu thập kết quả.
**Mã nguồn Java:**
```java
import java.util.*;

public class MultiPatternSearch {
    static class Node {
        Node[] children = new Node[26];
        Node fail = null;
        Node dict = null;
        List<String> output = new ArrayList<>();
    }

    public Map<String, List<Integer>> searchWords(String text, String[] words) {
        Node root = new Node();
        for (String word : words) {
            Node curr = root;
            for (char c : word.toCharArray()) {
                int idx = c - 'a';
                if (curr.children[idx] == null) curr.children[idx] = new Node();
                curr = curr.children[idx];
            }
            curr.output.add(word);
        }

        Queue<Node> q = new LinkedList<>();
        for (int i = 0; i < 26; i++) {
            if (root.children[i] != null) {
                root.children[i].fail = root;
                q.add(root.children[i]);
            } else {
                root.children[i] = root;
            }
        }

        while (!q.isEmpty()) {
            Node u = q.poll();
            for (int i = 0; i < 26; i++) {
                if (u.children[i] != null && u.children[i] != root) {
                    Node v = u.children[i];
                    Node failNode = u.fail;
                    v.fail = failNode.children[i];
                    v.dict = v.fail.output.isEmpty() ? v.fail.dict : v.fail;
                    q.add(v);
                } else {
                    u.children[i] = u.fail.children[i];
                }
            }
        }

        Map<String, List<Integer>> result = new HashMap<>();
        Node curr = root;
        for (int i = 0; i < text.length(); i++) {
            curr = curr.children[text.charAt(i) - 'a'];
            Node temp = curr;
            while (temp != null && temp != root) {
                for (String w : temp.output) {
                    result.computeIfAbsent(w, k -> new ArrayList<>()).add(i - w.length() + 1);
                }
                temp = temp.dict;
            }
        }
        return result;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N + M + Z)$ với $N$ là độ dài text, $M$ là tổng độ dài words, $Z$ là số lượng kết quả. Không gian: $O(M \cdot 26)$.

---

## 2. Stream of Characters
**Đề bài chi tiết:** Thiết kế một cấu trúc dữ liệu chấp nhận một luồng các ký tự và kiểm tra xem có bất kỳ hậu tố nào của luồng đã nhập khớp với bất kỳ từ nào trong từ điển cho trước không.
**Phân tích thuật toán:** Thay vì duyệt lại mỗi lần có ký tự mới, ta duy trì trạng thái của Aho-Corasick Automaton. Mỗi lần nhận một ký tự, ta di chuyển trạng thái hiện tại trên Automaton và kiểm tra xem node hiện tại (hoặc qua dictionary links) có kết quả hay không.
**Mã nguồn Java:**
```java
import java.util.*;

class StreamChecker {
    static class Node {
        Node[] children = new Node[26];
        Node fail = null;
        boolean isWord = false;
    }

    private Node root;
    private Node curr;

    public StreamChecker(String[] words) {
        root = new Node();
        curr = root;
        for (String word : words) {
            Node node = root;
            for (char c : word.toCharArray()) {
                if (node.children[c - 'a'] == null) {
                    node.children[c - 'a'] = new Node();
                }
                node = node.children[c - 'a'];
            }
            node.isWord = true;
        }
        buildAhoCorasick();
    }

    private void buildAhoCorasick() {
        Queue<Node> q = new LinkedList<>();
        for (int i = 0; i < 26; i++) {
            if (root.children[i] != null) {
                root.children[i].fail = root;
                q.add(root.children[i]);
            } else {
                root.children[i] = root;
            }
        }
        while (!q.isEmpty()) {
            Node u = q.poll();
            for (int i = 0; i < 26; i++) {
                if (u.children[i] != null && u.children[i] != root) {
                    Node v = u.children[i];
                    v.fail = u.fail.children[i];
                    v.isWord |= v.fail.isWord; // Chuyển giao isWord từ fail link
                    q.add(v);
                } else {
                    u.children[i] = u.fail.children[i];
                }
            }
        }
    }

    public boolean query(char letter) {
        curr = curr.children[letter - 'a'];
        return curr.isWord;
    }
}
```
**Độ phức tạp:** Thời gian Query: $O(1)$ do đã tối ưu transitions. Xây dựng: $O(M \cdot 26)$. Không gian: $O(M \cdot 26)$.

---

## 3. Word Break II (Aho-Corasick approach)
**Đề bài chi tiết:** Cho một chuỗi `s` và từ điển `wordDict`, hãy thêm khoảng trắng vào `s` để tạo thành một câu hợp lệ mà mỗi từ đều thuộc từ điển. Trả về tất cả các câu có thể tạo được.
**Phân tích thuật toán:** Mặc dù thường giải bằng DP + Backtracking, ta có thể dùng Aho-Corasick để tìm trước mọi từ trong từ điển kết thúc tại mỗi vị trí `i` của `s`. Từ đó, xây dựng đồ thị nhảy bước và dùng DFS để tìm toàn bộ đường đi.
**Mã nguồn Java:**
```java
import java.util.*;

public class WordBreakII {
    static class Node {
        Node[] children = new Node[26];
        Node fail = null;
        List<String> words = new ArrayList<>();
    }

    public List<String> wordBreak(String s, List<String> wordDict) {
        Node root = new Node();
        for (String w : wordDict) {
            Node node = root;
            for (char c : w.toCharArray()) {
                if (node.children[c - 'a'] == null) node.children[c - 'a'] = new Node();
                node = node.children[c - 'a'];
            }
            node.words.add(w);
        }

        Queue<Node> q = new LinkedList<>();
        for (int i = 0; i < 26; i++) {
            if (root.children[i] != null) {
                root.children[i].fail = root;
                q.add(root.children[i]);
            } else {
                root.children[i] = root;
            }
        }

        while (!q.isEmpty()) {
            Node u = q.poll();
            for (int i = 0; i < 26; i++) {
                if (u.children[i] != null && u.children[i] != root) {
                    Node v = u.children[i];
                    v.fail = u.fail.children[i];
                    v.words.addAll(v.fail.words);
                    q.add(v);
                } else {
                    u.children[i] = u.fail.children[i];
                }
            }
        }

        List<String>[] endsAt = new List[s.length()];
        for (int i = 0; i < s.length(); i++) endsAt[i] = new ArrayList<>();

        Node curr = root;
        for (int i = 0; i < s.length(); i++) {
            curr = curr.children[s.charAt(i) - 'a'];
            for (String w : curr.words) {
                endsAt[i].add(w);
            }
        }

        List<String> res = new ArrayList<>();
        dfs(s.length() - 1, endsAt, "", res);
        return res;
    }

    private void dfs(int endIdx, List<String>[] endsAt, String currentPath, List<String> res) {
        if (endIdx < 0) {
            res.add(currentPath.trim());
            return;
        }
        for (String w : endsAt[endIdx]) {
            dfs(endIdx - w.length(), endsAt, w + (currentPath.isEmpty() ? "" : " " + currentPath), res);
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(N^2 + 2^N)$ trong trường hợp xấu nhất (nhiều đáp án). Không gian: $O(M \cdot 26 + N \cdot K)$ với $K$ là số từ tại một điểm.

---

## 4. Minimum Number of Valid Strings to Form Target
**Đề bài chi tiết:** Cho chuỗi target và một danh sách words, tìm số lượng chuỗi nhỏ nhất từ words ghép lại thành target. (Có thể coi đây là bài toán ghép tiền tố).
**Phân tích thuật toán:** Sử dụng DP + Aho-Corasick. Tìm độ dài từ dài nhất tại mỗi index `i` của `target`. Với mỗi bước trên Automaton, lấy max độ dài các từ khớp, chuyển DP qua bước sau: `dp[i] = min(dp[i], dp[i - len] + 1)`.
**Mã nguồn Java:**
```java
import java.util.*;

public class MinValidStrings {
    static class Node {
        Node[] children = new Node[26];
        Node fail;
        int maxLen = 0;
    }

    public int minValidStrings(String[] words, String target) {
        Node root = new Node();
        for (String w : words) {
            Node curr = root;
            int len = 0;
            for (char c : w.toCharArray()) {
                if (curr.children[c - 'a'] == null) curr.children[c - 'a'] = new Node();
                curr = curr.children[c - 'a'];
                len++;
                curr.maxLen = Math.max(curr.maxLen, len);
            }
        }

        Queue<Node> q = new LinkedList<>();
        for (int i = 0; i < 26; i++) {
            if (root.children[i] != null) {
                root.children[i].fail = root;
                q.add(root.children[i]);
            } else {
                root.children[i] = root;
            }
        }
        while (!q.isEmpty()) {
            Node u = q.poll();
            for (int i = 0; i < 26; i++) {
                if (u.children[i] != null && u.children[i] != root) {
                    Node v = u.children[i];
                    v.fail = u.fail.children[i];
                    v.maxLen = Math.max(v.maxLen, v.fail.maxLen);
                    q.add(v);
                } else {
                    u.children[i] = u.fail.children[i];
                }
            }
        }

        int n = target.length();
        int[] dp = new int[n + 1];
        Arrays.fill(dp, Integer.MAX_VALUE / 2);
        dp[0] = 0;
        
        Node curr = root;
        for (int i = 0; i < n; i++) {
            curr = curr.children[target.charAt(i) - 'a'];
            int maxMatch = curr.maxLen;
            if (maxMatch > 0) {
                // Tối ưu hóa: Thay vì update 1 điểm, có thể update range bằng Segment Tree 
                // hoặc BFS. Dưới đây là cách update đơn giản cho độ dài nhỏ.
                for(int len = 1; len <= maxMatch; len++) {
                   if (i - len + 1 >= 0) {
                       dp[i + 1] = Math.min(dp[i + 1], dp[i + 1 - len] + 1);
                   }
                }
            }
        }
        return dp[n] >= Integer.MAX_VALUE / 2 ? -1 : dp[n];
    }
}
```
**Độ phức tạp:** Thời gian: Xây dựng $O(M)$, Tìm kiếm DP $O(N \cdot \text{maxLen})$. Không gian $O(M + N)$.

---

## 5. Find All Words in a String
**Đề bài chi tiết:** Trả về một tập hợp (Set) các từ duy nhất từ dictionary xuất hiện ở bất cứ đâu trong chuỗi `text` đầu vào.
**Phân tích thuật toán:** Một ứng dụng trực tiếp của thuật toán Aho-Corasick. Khi duyệt chuỗi, hễ dictionary link trỏ tới node chứa từ, ta đẩy thẳng từ đó vào HashSet. Cuối cùng trả về Set đó.
**Mã nguồn Java:**
```java
import java.util.*;

public class FindAllWords {
    static class Node {
        Map<Character, Node> children = new HashMap<>();
        Node fail;
        Node dict;
        String word = null;
    }
    
    public Set<String> find(String text, String[] dict) {
        Node root = new Node();
        for (String w : dict) {
            Node curr = root;
            for (char c : w.toCharArray()) {
                curr.children.putIfAbsent(c, new Node());
                curr = curr.children.get(c);
            }
            curr.word = w;
        }
        
        Queue<Node> q = new LinkedList<>();
        for (Node child : root.children.values()) {
            child.fail = root;
            q.add(child);
        }
        
        while (!q.isEmpty()) {
            Node u = q.poll();
            for (Map.Entry<Character, Node> e : u.children.entrySet()) {
                char c = e.getKey();
                Node v = e.getValue();
                
                Node failNode = u.fail;
                while (failNode != null && !failNode.children.containsKey(c)) {
                    failNode = failNode.fail;
                }
                v.fail = failNode == null ? root : failNode.children.get(c);
                v.dict = v.fail.word != null ? v.fail : v.fail.dict;
                q.add(v);
            }
        }
        
        Set<String> res = new HashSet<>();
        Node curr = root;
        for (char c : text.toCharArray()) {
            while (curr != null && !curr.children.containsKey(c)) {
                curr = curr.fail;
            }
            curr = curr == null ? root : curr.children.get(c);
            
            Node temp = curr;
            while (temp != null) {
                if (temp.word != null) res.add(temp.word);
                temp = temp.dict;
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N + M + Z)$ amortized. Không gian: $O(M \cdot |\Sigma|)$.

---

## 6. Replace Words
**Đề bài chi tiết:** Cho một từ điển các tiền tố (roots), và một câu (sentence). Thay thế các từ trong câu bằng tiền tố ngắn nhất khớp với từ đó trong từ điển.
**Phân tích thuật toán:** Bài này chỉ cần tìm tiền tố ngắn nhất. Sử dụng một Trie bình thường là đủ, nhưng để hoàn thiện tư duy, ta thấy khi dùng Trie ta dừng ngay tại ký tự mà cờ `isWord = true` xuất hiện đầu tiên trên đường đi. 
**Mã nguồn Java:**
```java
import java.util.*;

public class ReplaceWords {
    static class TrieNode {
        TrieNode[] children = new TrieNode[26];
        String word;
    }
    
    public String replaceWords(List<String> dictionary, String sentence) {
        TrieNode root = new TrieNode();
        for (String word : dictionary) {
            TrieNode curr = root;
            for (char c : word.toCharArray()) {
                if (curr.children[c - 'a'] == null) curr.children[c - 'a'] = new TrieNode();
                curr = curr.children[c - 'a'];
            }
            curr.word = word;
        }
        
        StringBuilder ans = new StringBuilder();
        for (String word : sentence.split("\\s+")) {
            if (ans.length() > 0) ans.append(" ");
            
            TrieNode curr = root;
            for (char c : word.toCharArray()) {
                if (curr.children[c - 'a'] == null || curr.word != null) {
                    break;
                }
                curr = curr.children[c - 'a'];
            }
            ans.append(curr.word != null ? curr.word : word);
        }
        return ans.toString();
    }
}
```
**Độ phức tạp:** Thời gian: $O(N + M)$, $M$ là chiều dài dictionary, $N$ là tổng số ký tự sentence. Không gian: $O(M)$.

---

## 7. Concatenated Words
**Đề bài chi tiết:** Cho danh sách các từ. Trả về tất cả các từ có thể được tạo thành bằng cách ghép ít nhất 2 từ ngắn hơn trong chính danh sách đó.
**Phân tích thuật toán:** Sort danh sách từ theo độ dài. Dùng Trie để thêm dần các từ ngắn. Khi duyệt đến từ dài hơn, chạy DFS/DP kết hợp Trie để kiểm tra xem từ đó có thể tạo từ các từ đã có trong Trie không.
**Mã nguồn Java:**
```java
import java.util.*;

public class ConcatenatedWords {
    static class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean isEnd = false;
    }
    
    TrieNode root = new TrieNode();
    
    public List<String> findAllConcatenatedWordsInADict(String[] words) {
        Arrays.sort(words, (a, b) -> a.length() - b.length());
        List<String> ans = new ArrayList<>();
        for (String w : words) {
            if (w.isEmpty()) continue;
            if (canForm(w, 0)) {
                ans.add(w);
            } else {
                addWord(w);
            }
        }
        return ans;
    }
    
    private void addWord(String w) {
        TrieNode curr = root;
        for (char c : w.toCharArray()) {
            if (curr.children[c - 'a'] == null) curr.children[c - 'a'] = new TrieNode();
            curr = curr.children[c - 'a'];
        }
        curr.isEnd = true;
    }
    
    private boolean canForm(String w, int index) {
        if (index == w.length()) return true;
        TrieNode curr = root;
        for (int i = index; i < w.length(); i++) {
            curr = curr.children[w.charAt(i) - 'a'];
            if (curr == null) return false;
            if (curr.isEnd && canForm(w, i + 1)) {
                return true;
            }
        }
        return false;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N + N \cdot L^2)$ với $L$ là độ dài từ. Không gian: $O(M)$.

---

## 8. Longest Word in Dictionary
**Đề bài chi tiết:** Tìm từ dài nhất trong `words` mà tất cả các tiền tố của nó cũng đều nằm trong `words`. Nếu có nhiều kết quả, chọn từ nhỏ hơn theo thứ tự từ điển.
**Phân tích thuật toán:** Xây dựng Trie chứa tất cả các từ. Sau đó thực hiện BFS hoặc DFS. Khi DFS, chỉ duyệt các nhánh mà `isEnd == true`. Giữ lại từ dài nhất và lexicographically nhỏ nhất.
**Mã nguồn Java:**
```java
import java.util.*;

public class LongestWord {
    static class Node {
        Node[] children = new Node[26];
        String word = "";
    }
    
    private String res = "";
    
    public String longestWord(String[] words) {
        Node root = new Node();
        for (String w : words) {
            Node curr = root;
            for (char c : w.toCharArray()) {
                if (curr.children[c - 'a'] == null) curr.children[c - 'a'] = new Node();
                curr = curr.children[c - 'a'];
            }
            curr.word = w;
        }
        dfs(root);
        return res;
    }
    
    private void dfs(Node node) {
        if (node.word.length() > res.length() || (node.word.length() == res.length() && node.word.compareTo(res) < 0)) {
            res = node.word;
        }
        for (int i = 0; i < 26; i++) {
            if (node.children[i] != null && !node.children[i].word.isEmpty()) {
                dfs(node.children[i]);
            }
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(M)$ với $M$ là tổng độ dài các chuỗi. Không gian: $O(M)$.

---

## 9. Autocomplete System
**Đề bài chi tiết:** Thiết kế một hệ thống Tự động hoàn tất (Autocomplete). Mỗi khi người dùng gõ 1 ký tự, hệ thống trả về top 3 câu phổ biến nhất bắt đầu bằng chuỗi người dùng đã nhập.
**Phân tích thuật toán:** Xây dựng Trie, mỗi node sẽ lưu một danh sách các câu đã kết thúc ở các nhánh bên dưới nó, cùng với tần suất xuất hiện. Khi gõ một ký tự, di chuyển con trỏ trong Trie và trả về danh sách đã được sắp xếp (Top 3).
**Mã nguồn Java:**
```java
import java.util.*;

class AutocompleteSystem {
    static class TrieNode {
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
**Độ phức tạp:** Thời gian truy vấn $O(L + K \log K)$, không gian $O(M)$.

---

## 10. Implement Trie (Prefix Tree) as basis for Aho-Corasick
**Đề bài chi tiết:** Cài đặt cấu trúc Trie hỗ trợ chèn một từ, tìm một từ chính xác, và kiểm tra một tiền tố có tồn tại hay không. (Đây là khung lõi để xây dựng Automaton).
**Phân tích thuật toán:** Sử dụng Mảng 26 ký tự hoặc HashMap cho children. Thêm cờ `isEnd`.
**Mã nguồn Java:**
```java
class Trie {
    class Node {
        Node[] children = new Node[26];
        boolean isEnd = false;
    }
    
    private final Node root;

    public Trie() {
        root = new Node();
    }
    
    public void insert(String word) {
        Node curr = root;
        for(char c : word.toCharArray()) {
            if (curr.children[c - 'a'] == null) {
                curr.children[c - 'a'] = new Node();
            }
            curr = curr.children[c - 'a'];
        }
        curr.isEnd = true;
    }
    
    public boolean search(String word) {
        Node node = searchPrefix(word);
        return node != null && node.isEnd;
    }
    
    public boolean startsWith(String prefix) {
        return searchPrefix(prefix) != null;
    }
    
    private Node searchPrefix(String word) {
        Node curr = root;
        for (char c : word.toCharArray()) {
            if (curr.children[c - 'a'] == null) return null;
            curr = curr.children[c - 'a'];
        }
        return curr;
    }
}
```
**Độ phức tạp:** Thời gian $O(L)$ cho mỗi thao tác, $L$ là chiều dài từ. Không gian $O(L)$.

---

## 11. Map Sum Pairs
**Đề bài chi tiết:** Thiết kế một cấu trúc dữ liệu `MapSum` cho phép chèn các cặp key-value (chuỗi và số nguyên) và tính tổng các giá trị của tất cả các key có chung một tiền tố cho trước.
**Phân tích thuật toán:** Sử dụng Trie. Mỗi node lưu tổng (`score`) của tất cả các từ đi qua nhánh đó. Để cập nhật đúng đắn khi một từ đã tồn tại bị chèn lại với giá trị mới, ta sử dụng một `HashMap` lưu các cặp key-value hiện tại, lấy phần chênh lệch (`val - old_val`) để cộng vào các node tương ứng trên Trie.
**Mã nguồn Java:**
```java
import java.util.*;

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
        TrieNode curr = root;
        for (char c : key.toCharArray()) {
            if (curr.children[c - 'a'] == null) {
                curr.children[c - 'a'] = new TrieNode();
            }
            curr = curr.children[c - 'a'];
            curr.score += delta;
        }
    }
    
    public int sum(String prefix) {
        TrieNode curr = root;
        for (char c : prefix.toCharArray()) {
            if (curr.children[c - 'a'] == null) {
                return 0;
            }
            curr = curr.children[c - 'a'];
        }
        return curr.score;
    }
}
```
**Độ phức tạp:** Thời gian: $O(L)$ với $L$ là độ dài của chuỗi cho cả chèn và tính tổng. Không gian: $O(N \cdot L)$ trong đó $N$ là số lượng chuỗi.

---

## 12. Maximum XOR of Two Numbers in an Array
**Đề bài chi tiết:** Cho một mảng các số nguyên. Tìm giá trị lớn nhất của `nums[i] ^ nums[j]` (phép toán XOR bitwise) trong đó `0 <= i, j < n`.
**Phân tích thuật toán:** Sử dụng Trie nhị phân. Đưa các bit của mỗi số (từ cao tới thấp, 31 về 0) vào Trie. Sau đó duyệt từng số trong mảng, cố gắng duyệt trên Trie ngược lại với từng bit của số hiện tại (ví dụ bit là 0 thì chọn nhánh 1, bit 1 thì chọn nhánh 0) để tối đa hóa các bit 1 trong kết quả XOR.
**Mã nguồn Java:**
```java
class MaximumXOR {
    class TrieNode {
        TrieNode[] children = new TrieNode[2];
    }
    
    private TrieNode root;
    
    public int findMaximumXOR(int[] nums) {
        root = new TrieNode();
        for (int num : nums) {
            TrieNode curr = root;
            for (int i = 31; i >= 0; i--) {
                int bit = (num >> i) & 1;
                if (curr.children[bit] == null) {
                    curr.children[bit] = new TrieNode();
                }
                curr = curr.children[bit];
            }
        }
        
        int max = 0;
        for (int num : nums) {
            TrieNode curr = root;
            int currentXor = 0;
            for (int i = 31; i >= 0; i--) {
                int bit = (num >> i) & 1;
                int toggledBit = 1 - bit;
                if (curr.children[toggledBit] != null) {
                    currentXor |= (1 << i);
                    curr = curr.children[toggledBit];
                } else {
                    curr = curr.children[bit];
                }
            }
            max = Math.max(max, currentXor);
        }
        return max;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log(\max A)) \approx O(32 \cdot N)$, Không gian: $O(N \log(\max A))$.

---

## 13. Word Search II
**Đề bài chi tiết:** Cho một bảng chữ cái (matrix 2D) và một danh sách các từ, tìm tất cả các từ từ danh sách có mặt trong bảng bằng cách nối các ô kề nhau.
**Phân tích thuật toán:** Chèn tất cả các từ cần tìm vào Trie để dễ dàng tra cứu đường đi tiền tố. Duyệt DFS từ mỗi ô trong bảng, nhảy qua các nhánh của Trie. Để tránh thăm lại ô đã duyệt, dùng cách đánh dấu tạm thời bảng (backtracking). Khi tìm thấy một từ, lưu nó vào tập kết quả.
**Mã nguồn Java:**
```java
import java.util.*;

public class WordSearchII {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        String word = null;
    }
    
    public List<String> findWords(char[][] board, String[] words) {
        TrieNode root = new TrieNode();
        for (String w : words) {
            TrieNode curr = root;
            for (char c : w.toCharArray()) {
                if (curr.children[c - 'a'] == null) curr.children[c - 'a'] = new TrieNode();
                curr = curr.children[c - 'a'];
            }
            curr.word = w;
        }
        
        Set<String> res = new HashSet<>();
        int rows = board.length, cols = board[0].length;
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                dfs(board, i, j, root, res);
            }
        }
        return new ArrayList<>(res);
    }
    
    private void dfs(char[][] board, int i, int j, TrieNode node, Set<String> res) {
        if (i < 0 || j < 0 || i >= board.length || j >= board[0].length || board[i][j] == '#') return;
        char c = board[i][j];
        if (node.children[c - 'a'] == null) return;
        
        node = node.children[c - 'a'];
        if (node.word != null) {
            res.add(node.word);
            node.word = null; // Tối ưu tránh trùng lặp
        }
        
        board[i][j] = '#'; // Mark visited
        dfs(board, i + 1, j, node, res);
        dfs(board, i - 1, j, node, res);
        dfs(board, i, j + 1, node, res);
        dfs(board, i, j - 1, node, res);
        board[i][j] = c; // Backtrack
    }
}
```
**Độ phức tạp:** Thời gian: $O(M \cdot 4^L)$ với $M$ là số ô trong bảng, $L$ là độ dài tối đa của từ. Không gian: $O(W \cdot L)$ cho Trie với $W$ là số từ.

---

## 14. Design Add and Search Words Data Structure
**Đề bài chi tiết:** Thiết kế một cấu trúc dữ liệu cho phép thêm các từ, và tìm kiếm từ, trong đó từ tìm kiếm có thể chứa ký tự dấu chấm `.` tương ứng với bất kỳ một ký tự chữ cái nào.
**Phân tích thuật toán:** Dùng Trie tiêu chuẩn cho hàm thêm. Đối với truy vấn tìm kiếm, ta dùng DFS để xử lý nếu có dấu `.`. Tại dấu `.`, đệ quy duyệt tất cả các con khác `null` của node hiện tại.
**Mã nguồn Java:**
```java
class WordDictionary {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean isEnd = false;
    }
    
    private TrieNode root;

    public WordDictionary() {
        root = new TrieNode();
    }
    
    public void addWord(String word) {
        TrieNode curr = root;
        for (char c : word.toCharArray()) {
            if (curr.children[c - 'a'] == null) curr.children[c - 'a'] = new TrieNode();
            curr = curr.children[c - 'a'];
        }
        curr.isEnd = true;
    }
    
    public boolean search(String word) {
        return searchInNode(word, 0, root);
    }
    
    private boolean searchInNode(String word, int index, TrieNode node) {
        if (index == word.length()) return node.isEnd;
        char c = word.charAt(index);
        
        if (c == '.') {
            for (int i = 0; i < 26; i++) {
                if (node.children[i] != null && searchInNode(word, index + 1, node.children[i])) {
                    return true;
                }
            }
            return false;
        } else {
            if (node.children[c - 'a'] == null) return false;
            return searchInNode(word, index + 1, node.children[c - 'a']);
        }
    }
}
```
**Độ phức tạp:** Thời gian thêm: $O(L)$, tìm kiếm: Xấu nhất $O(26^L)$ khi chuỗi toàn `.` nhưng thực tế sẽ nhanh hơn nhờ tỉa nhánh. Không gian: $O(N \cdot L)$.

---

## 15. Camelcase Matching
**Đề bài chi tiết:** Cho một mẫu `pattern` có các chữ hoa/thường, và một danh sách `queries`. Kiểm tra xem nếu ta thêm một số ký tự thường vào `pattern` thì có tạo thành một chuỗi thuộc `queries` được hay không.
**Phân tích thuật toán:** Ta có thể cài đặt bằng con trỏ để kiểm tra từng query một. Duyệt qua ký tự của query, nếu khớp với ký tự tại con trỏ của pattern thì duyệt tiếp. Nếu không khớp mà lại là chữ hoa thì là sai. Kết thúc, nếu pattern đã chạy hết mà query còn thừa chữ hoa, thì trả về sai.
**Mã nguồn Java:**
```java
import java.util.*;

public class CamelcaseMatching {
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
**Độ phức tạp:** Thời gian: $O(Q \cdot L)$ với $Q$ là số query, $L$ là chiều dài trung bình. Không gian: $O(1)$.

---

## 16. Palindrome Pairs
**Đề bài chi tiết:** Cho danh sách các từ. Trả về tất cả các cặp chỉ số `(i, j)` sao cho khi ghép 2 từ `words[i] + words[j]` lại ta được một chuỗi đối xứng (palindrome).
**Phân tích thuật toán:** Sử dụng Trie. Thêm các từ đảo ngược vào Trie, lưu trữ `index` của từ tại node cuối. Đồng thời, tại mỗi node, lưu danh sách các index của các từ mà phần chưa xử lý của chúng là palindrome. Sau đó, duyệt mỗi từ và tìm kiếm trên Trie để ghép cặp.
**Mã nguồn Java:**
```java
import java.util.*;

public class PalindromePairs {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        int wordIndex = -1;
        List<Integer> palindromePrefixes = new ArrayList<>();
    }
    
    private TrieNode root = new TrieNode();
    
    private boolean isPalindrome(String s, int left, int right) {
        while (left < right) {
            if (s.charAt(left++) != s.charAt(right--)) return false;
        }
        return true;
    }
    
    public List<List<Integer>> palindromePairs(String[] words) {
        for (int i = 0; i < words.length; i++) {
            String word = words[i];
            TrieNode curr = root;
            for (int j = word.length() - 1; j >= 0; j--) {
                if (isPalindrome(word, 0, j)) {
                    curr.palindromePrefixes.add(i);
                }
                int idx = word.charAt(j) - 'a';
                if (curr.children[idx] == null) curr.children[idx] = new TrieNode();
                curr = curr.children[idx];
            }
            curr.wordIndex = i;
            curr.palindromePrefixes.add(i);
        }
        
        List<List<Integer>> res = new ArrayList<>();
        for (int i = 0; i < words.length; i++) {
            String word = words[i];
            TrieNode curr = root;
            for (int j = 0; j < word.length(); j++) {
                if (curr.wordIndex != -1 && curr.wordIndex != i && isPalindrome(word, j, word.length() - 1)) {
                    res.add(Arrays.asList(i, curr.wordIndex));
                }
                curr = curr.children[word.charAt(j) - 'a'];
                if (curr == null) break;
            }
            if (curr != null) {
                for (int j : curr.palindromePrefixes) {
                    if (i != j) {
                        res.add(Arrays.asList(i, j));
                    }
                }
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \cdot L^2)$ với $N$ là số từ, $L$ là chiều dài. Không gian: $O(N \cdot L^2)$ để lưu các palindromePrefixes trong Trie.

---

## 17. Magic Dictionary
**Đề bài chi tiết:** Thiết kế một cấu trúc hỗ trợ thêm các từ, và cung cấp hàm `search`. Hàm này sẽ trả về true nếu ta có thể thay đổi đúng 1 ký tự trong truy vấn để trở thành một từ hợp lệ trong từ điển.
**Phân tích thuật toán:** Lưu các từ trong Trie. Khi kiểm tra search, sử dụng DFS kèm theo trạng thái biến đổi số lượng lỗi. Bắt đầu với `errors = 1`. Nếu sai khác, giảm errors, nếu errors < 0 thì cắt nhánh. Khi kết thúc chuỗi phải đảm bảo `errors == 0` và `isEnd == true`.
**Mã nguồn Java:**
```java
class MagicDictionary {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean isEnd = false;
    }
    
    private TrieNode root;

    public MagicDictionary() {
        root = new TrieNode();
    }
    
    public void buildDict(String[] dictionary) {
        for (String w : dictionary) {
            TrieNode curr = root;
            for (char c : w.toCharArray()) {
                if (curr.children[c - 'a'] == null) curr.children[c - 'a'] = new TrieNode();
                curr = curr.children[c - 'a'];
            }
            curr.isEnd = true;
        }
    }
    
    public boolean search(String searchWord) {
        return dfs(root, searchWord, 0, 1);
    }
    
    private boolean dfs(TrieNode node, String word, int index, int mistakesLeft) {
        if (index == word.length()) {
            return mistakesLeft == 0 && node.isEnd;
        }
        
        char c = word.charAt(index);
        for (int i = 0; i < 26; i++) {
            if (node.children[i] != null) {
                if (i == c - 'a') {
                    if (dfs(node.children[i], word, index + 1, mistakesLeft)) return true;
                } else if (mistakesLeft > 0) {
                    if (dfs(node.children[i], word, index + 1, mistakesLeft - 1)) return true;
                }
            }
        }
        return false;
    }
}
```
**Độ phức tạp:** Thời gian truy vấn $O(26 \cdot L)$ vì chỉ có duy nhất 1 sai sót cho phép. Không gian $O(N \cdot L)$.

---

## 18. Index Pairs of a String
**Đề bài chi tiết:** Cho chuỗi văn bản và danh sách các từ (words). Tìm tất cả các đoạn `[start, end]` mà `text.substring(start, end+1)` khớp với một từ trong mảng words.
**Phân tích thuật toán:** Một bài áp dụng thuần túy Automaton (Aho-Corasick) hoặc Trie. Ta có thể duyệt mỗi index trong văn bản như một `start` và tìm đường trong Trie để bắt tất cả những từ kết thúc ở `end`. Cuối cùng, trả về mảng các khoảng và sắp xếp chúng.
**Mã nguồn Java:**
```java
import java.util.*;

public class IndexPairs {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean isEnd = false;
    }
    
    public int[][] indexPairs(String text, String[] words) {
        TrieNode root = new TrieNode();
        for (String w : words) {
            TrieNode curr = root;
            for (char c : w.toCharArray()) {
                if (curr.children[c - 'a'] == null) curr.children[c - 'a'] = new TrieNode();
                curr = curr.children[c - 'a'];
            }
            curr.isEnd = true;
        }
        
        List<int[]> res = new ArrayList<>();
        for (int i = 0; i < text.length(); i++) {
            TrieNode curr = root;
            for (int j = i; j < text.length(); j++) {
                curr = curr.children[text.charAt(j) - 'a'];
                if (curr == null) break;
                if (curr.isEnd) {
                    res.add(new int[]{i, j});
                }
            }
        }
        
        int[][] ans = new int[res.size()][2];
        for (int i = 0; i < res.size(); i++) ans[i] = res.get(i);
        return ans;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N^2 + M)$, vì sử dụng vòng lặp duyệt Trie từ mọi `start`. Nếu chuỗi cực lớn, có thể dùng Aho-Corasick để đạt $O(N + Z)$ (Z là số lượng match). Không gian: $O(M)$.

---

## 19. Multi-Search LCCI
**Đề bài chi tiết:** Cho chuỗi mẹ `big` và mảng chuỗi con `smalls`. Nhiệm vụ là tìm vị trí xuất hiện của mỗi `small` trong `big`.
**Phân tích thuật toán:** Về lý thuyết ta dùng Aho-Corasick hoặc Trie tìm kiếm từng hậu tố của `big`. Ở đây, ta dùng cách Aho-Corasick để tối ưu hóa thời gian tuyến tính hoặc dùng Trie bình thường cho đơn giản. Dưới đây áp dụng Trie: thêm các chuỗi `smalls` vào Trie (lưu lại index gốc của từ đó).
**Mã nguồn Java:**
```java
import java.util.*;

public class MultiSearch {
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
                curr = curr.children[big.charAt(j) - 'a'];
                if (curr == null) break;
                if (curr.wordIndex != -1) {
                    resList[curr.wordIndex].add(i);
                }
            }
        }
        
        int[][] res = new int[smalls.length][];
        for (int i = 0; i < smalls.length; i++) {
            if (smalls[i].isEmpty()) {
                res[i] = new int[0];
                continue;
            }
            res[i] = new int[resList[i].size()];
            for (int j = 0; j < resList[i].size(); j++) {
                res[i][j] = resList[i].get(j);
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N^2 + M \cdot L)$, Không gian: $O(M \cdot L + Z)$ (Z là số matches).

---

## 20. Substring Matching at Scale
**Đề bài chi tiết:** Xử lý chuỗi lớn và số lượng lớn từ khóa. Nâng cấp Aho-Corasick State Machine để việc transition state luôn là thời gian hằng số chuẩn $O(1)$ thay vì amortized.
**Phân tích thuật toán:** Để tạo một Deterministic Finite Automaton (DFA) từ Nondeterministic Finite Automaton (Trie), trong BFS duyệt fail link, thay vì chỉ tạo liên kết fail, ta biến đổi mảng con `children` bằng cách: với mọi ký tự c (0-25), nếu con tồn tại, cập nhật fail của nó, ngược lại gán trực tiếp `children[c] = fail.children[c]`. Điều này phá hủy cấu trúc Tree nguyên bản để làm một Dense Graph State Machine, đảm bảo mọi input state transition chỉ bằng phép gán `curr = curr.children[c]`.
**Mã nguồn Java:**
```java
import java.util.*;

public class DfaAhoCorasick {
    static class StateNode {
        StateNode[] next = new StateNode[26];
        StateNode fail = null;
        List<String> matchedWords = new ArrayList<>();
    }
    
    public void buildDFA(String[] words) {
        StateNode root = new StateNode();
        for (String w : words) {
            StateNode curr = root;
            for (char c : w.toCharArray()) {
                int idx = c - 'a';
                if (curr.next[idx] == null) {
                    curr.next[idx] = new StateNode();
                }
                curr = curr.next[idx];
            }
            curr.matchedWords.add(w);
        }
        
        Queue<StateNode> q = new LinkedList<>();
        for (int i = 0; i < 26; i++) {
            if (root.next[i] != null) {
                root.next[i].fail = root;
                q.add(root.next[i]);
            } else {
                root.next[i] = root; // Trỏ về root nếu rỗng
            }
        }
        
        while (!q.isEmpty()) {
            StateNode u = q.poll();
            for (int i = 0; i < 26; i++) {
                if (u.next[i] != null && u.next[i] != root) {
                    StateNode v = u.next[i];
                    v.fail = u.fail.next[i];
                    v.matchedWords.addAll(v.fail.matchedWords);
                    q.add(v);
                } else {
                    // Cập nhật mảng trực tiếp cho bước nhảy O(1)
                    u.next[i] = u.fail.next[i];
                }
            }
        }
    }
}
```
**Độ phức tạp:** Thời gian xây dựng DFA: $O(M \cdot \Sigma)$, Duyệt DFA $O(N)$, luôn đảm bảo nhảy state chỉ 1 phép gán (nghiêm ngặt $O(1)$ per char).

---

## 21. Implement Double-Array Trie
**Đề bài chi tiết:** Cài đặt Double-Array Trie (DAT) - một cấu trúc dữ liệu Trie được làm phẳng thành hai mảng `base` và `check`. Nó giúp giảm đáng kể bộ nhớ so với Trie bằng con trỏ thông thường, đồng thời vẫn giữ được tốc độ truy xuất $O(1)$ cho mỗi bước nhảy. Cấu trúc này thường được dùng làm nền tảng cho Aho-Corasick ở cấp độ production.
**Phân tích thuật toán:** Một node `s` chuyển sang node `t` bằng ký tự `c` nếu thỏa mãn `base[s] + c = t` và `check[t] = s`. Quá trình chèn từ yêu cầu tìm một `base` phù hợp sao cho mọi con của `s` không bị đụng độ với các chỉ số đã có. Tại đây ta minh họa cấu trúc truy vấn.
**Mã nguồn Java:**
```java
import java.util.*;

public class DoubleArrayTrie {
    int[] base;
    int[] check;
    boolean[] isEnd;
    
    public DoubleArrayTrie(int size) {
        base = new int[size];
        check = new int[size];
        isEnd = new boolean[size];
        Arrays.fill(check, -1);
        base[0] = 1; 
        check[0] = 0;
    }
    
    public void buildSimple(List<String> words) {
        // Simplified dynamic insertion is extremely slow. 
        // Usually built via BFS from a standard Trie.
    }
    
    public boolean search(String word) {
        int s = 0;
        for (char c : word.toCharArray()) {
            int offset = c - 'a';
            int t = base[s] + offset;
            if (t >= check.length || check[t] != s) return false;
            s = t;
        }
        return isEnd[s];
    }
}
```
**Độ phức tạp:** Thời gian tìm kiếm: $O(L)$ với một phép tính mảng đơn giản mỗi bước. Không gian: Khoảng 2-3 mảng số nguyên tuyến tính so với hàng ngàn object references của Trie con trỏ.

---

## 22. DNA Sequence Search
**Đề bài chi tiết:** Cho một chuỗi DNA lớn (chỉ chứa 'A', 'C', 'G', 'T') và một danh sách các mẫu DNA ngắn. Tìm tất cả các mẫu DNA ngắn có xuất hiện trong chuỗi DNA lớn.
**Phân tích thuật toán:** Thuật toán Aho-Corasick với bảng chữ cái $\Sigma = 4$ (A, C, G, T) để tối ưu bộ nhớ. Ta ánh xạ A=0, C=1, G=2, T=3. Việc dùng Aho-Corasick giúp tìm được nhiều mẫu DNA cùng lúc chỉ trong một lần duyệt qua chuỗi lớn, vượt trội so với Rabin-Karp hay KMP khi có rất nhiều mẫu.
**Mã nguồn Java:**
```java
import java.util.*;

public class DnaAhoCorasick {
    static class Node {
        Node[] children = new Node[4];
        Node fail;
        List<String> patterns = new ArrayList<>();
    }
    
    private int getIndex(char c) {
        if (c == 'A') return 0;
        if (c == 'C') return 1;
        if (c == 'G') return 2;
        return 3;
    }
    
    public List<String> searchDNA(String genome, String[] patterns) {
        Node root = new Node();
        for (String p : patterns) {
            Node curr = root;
            for (char c : p.toCharArray()) {
                int idx = getIndex(c);
                if (curr.children[idx] == null) curr.children[idx] = new Node();
                curr = curr.children[idx];
            }
            curr.patterns.add(p);
        }
        
        Queue<Node> q = new LinkedList<>();
        for (int i = 0; i < 4; i++) {
            if (root.children[i] != null) {
                root.children[i].fail = root;
                q.add(root.children[i]);
            } else {
                root.children[i] = root;
            }
        }
        
        while (!q.isEmpty()) {
            Node u = q.poll();
            for (int i = 0; i < 4; i++) {
                if (u.children[i] != null && u.children[i] != root) {
                    Node v = u.children[i];
                    v.fail = u.fail.children[i];
                    v.patterns.addAll(v.fail.patterns);
                    q.add(v);
                } else {
                    u.children[i] = u.fail.children[i];
                }
            }
        }
        
        Set<String> res = new HashSet<>();
        Node curr = root;
        for (char c : genome.toCharArray()) {
            curr = curr.children[getIndex(c)];
            res.addAll(curr.patterns);
        }
        return new ArrayList<>(res);
    }
}
```
**Độ phức tạp:** Thời gian: $O(N + M)$ trong đó $N$ là độ dài genome, $M$ là tổng độ dài các mẫu. Không gian $O(M)$.

---

## 23. Censor Profanity / Bad Words
**Đề bài chi tiết:** Cho một đoạn chat (văn bản) và một danh sách các từ khóa cấm (bad words). Hãy thay thế tất cả các từ cấm xuất hiện trong văn bản bằng các dấu `*` có cùng độ dài. Nếu các từ cấm chồng lặp, hãy ẩn toàn bộ vùng bị lặp.
**Phân tích thuật toán:** Dùng Aho-Corasick Automaton. Trong quá trình duyệt chuỗi, mỗi khi tìm thấy một từ cấm, ta có thể lưu lại khoảng `[start, end]`. Hoặc đơn giản hơn, tại mỗi node lưu độ dài từ lớn nhất, và khi tới một match, ta dùng mảng boolean đánh dấu các ký tự cần ẩn.
**Mã nguồn Java:**
```java
import java.util.*;

public class CensorProfanity {
    static class Node {
        Node[] children = new Node[26];
        Node fail;
        int maxLen = 0; // Lưu độ dài từ cấm dài nhất kết thúc tại đây
    }
    
    public String censor(String text, String[] badWords) {
        Node root = new Node();
        for (String w : badWords) {
            Node curr = root;
            for (char c : w.toCharArray()) {
                if (curr.children[c - 'a'] == null) curr.children[c - 'a'] = new Node();
                curr = curr.children[c - 'a'];
            }
            curr.maxLen = Math.max(curr.maxLen, w.length());
        }
        
        Queue<Node> q = new LinkedList<>();
        for (int i = 0; i < 26; i++) {
            if (root.children[i] != null) {
                root.children[i].fail = root;
                q.add(root.children[i]);
            } else {
                root.children[i] = root;
            }
        }
        
        while (!q.isEmpty()) {
            Node u = q.poll();
            for (int i = 0; i < 26; i++) {
                if (u.children[i] != null && u.children[i] != root) {
                    Node v = u.children[i];
                    v.fail = u.fail.children[i];
                    v.maxLen = Math.max(v.maxLen, v.fail.maxLen);
                    q.add(v);
                } else {
                    u.children[i] = u.fail.children[i];
                }
            }
        }
        
        int n = text.length();
        boolean[] mask = new boolean[n];
        Node curr = root;
        
        for (int i = 0; i < n; i++) {
            char c = text.charAt(i);
            if (c >= 'a' && c <= 'z') {
                curr = curr.children[c - 'a'];
                if (curr.maxLen > 0) {
                    for (int j = i - curr.maxLen + 1; j <= i; j++) {
                        mask[j] = true;
                    }
                }
            } else {
                curr = root; // Reset if not lowercase letter
            }
        }
        
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < n; i++) {
            if (mask[i]) sb.append('*');
            else sb.append(text.charAt(i));
        }
        return sb.toString();
    }
}
```
**Độ phức tạp:** Thời gian: $O(N + M)$ hoặc $O(N \cdot L)$ tùy chiến lược đánh dấu. Không gian: $O(M + N)$.

---

## 24. Longest Duplicate Substring
**Đề bài chi tiết:** Cho một chuỗi `s`, tìm chuỗi con dài nhất lặp lại ít nhất hai lần (có thể chồng lên nhau).
**Phân tích thuật toán:** Bài này thường giải bằng Binary Search + Hash (Rabin-Karp) hoặc Suffix Tree. Dưới góc nhìn Automaton (Aho-Corasick), cấu trúc tương đồng là Suffix Automaton. Tuy nhiên, cách dễ viết nhất trong Java là Binary Search + Rolling Hash.
**Mã nguồn Java:**
```java
import java.util.*;

public class LongestDuplicateSubstring {
    public String longestDupSubstring(String s) {
        int left = 1, right = s.length();
        String ans = "";
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            String dup = search(s, mid);
            if (dup != null) {
                ans = dup;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return ans;
    }
    
    private String search(String s, int len) {
        long q = (1L << 31) - 1;
        long h = 0, aL = 1, a = 26;
        for (int i = 0; i < len; i++) {
            h = (h * a + (s.charAt(i) - 'a')) % q;
            aL = (aL * a) % q;
        }
        
        Set<Long> seen = new HashSet<>();
        seen.add(h);
        
        for (int i = 1; i <= s.length() - len; i++) {
            h = (h * a - (s.charAt(i - 1) - 'a') * aL % q + q) % q;
            h = (h + (s.charAt(i + len - 1) - 'a')) % q;
            if (seen.contains(h)) {
                return s.substring(i, i + len);
            }
            seen.add(h);
        }
        return null;
    }
}
```
**Độ phức tạp:** Thời gian $O(N \log N)$ trung bình. Không gian $O(N)$.

---

## 25. Prefix and Suffix Search
**Đề bài chi tiết:** Thiết kế một cấu trúc dữ liệu khởi tạo bằng một danh sách các từ. Hỗ trợ truy vấn tìm `index` lớn nhất của từ trong danh sách mà có tiền tố `pref` và hậu tố `suff` cho trước.
**Phân tích thuật toán:** Kết hợp prefix và suffix bằng cách tạo một chuỗi khóa mới `suffix + '{' + prefix` và nhét vào Trie. Với mỗi từ `apple`, chèn `e{apple`, `le{apple`, `ple{apple`, `pple{apple`, `apple{apple` vào Trie. Ở mỗi node, lưu lại chỉ số lớn nhất của từ đi qua. Khi query, ta chỉ cần tìm kiếm chuỗi `suff + '{' + pref` trong Trie và trả về index tại node cuối.
**Mã nguồn Java:**
```java
class WordFilter {
    class TrieNode {
        TrieNode[] children = new TrieNode[27];
        int weight = -1;
    }
    
    TrieNode root;

    public WordFilter(String[] words) {
        root = new TrieNode();
        for (int weight = 0; weight < words.length; weight++) {
            String w = words[weight];
            for (int i = 0; i <= w.length(); i++) {
                String insertStr = w.substring(i) + "{" + w;
                TrieNode curr = root;
                curr.weight = weight;
                for (char c : insertStr.toCharArray()) {
                    int idx = c == '{' ? 26 : c - 'a';
                    if (curr.children[idx] == null) {
                        curr.children[idx] = new TrieNode();
                    }
                    curr = curr.children[idx];
                    curr.weight = weight;
                }
            }
        }
    }
    
    public int f(String pref, String suff) {
        TrieNode curr = root;
        String searchStr = suff + "{" + pref;
        for (char c : searchStr.toCharArray()) {
            int idx = c == '{' ? 26 : c - 'a';
            if (curr.children[idx] == null) return -1;
            curr = curr.children[idx];
        }
        return curr.weight;
    }
}
```
**Độ phức tạp:** Thời gian khởi tạo $O(N \cdot L^2)$, truy vấn $O(L)$. Không gian $O(N \cdot L^2)$.

---

## 26. Encrypt and Decrypt Strings
**Đề bài chi tiết:** Mã hóa từng ký tự bằng chuỗi 2 ký tự và giải mã. Khi giải mã, một chuỗi mã hóa có thể tương ứng với nhiều chuỗi gốc. Trả về số lượng chuỗi giải mã hợp lệ có tồn tại trong một từ điển (dictionary).
**Phân tích thuật toán:** Mã hóa thì dễ ($O(L)$). Giải mã để đếm số từ hợp lệ có thể sử dụng Trie để loại bỏ sớm các tiền tố không khả thi, hoặc đơn giản hơn là lưu tần suất các chuỗi đã mã hóa của từ điển vào HashMap và chỉ việc lookup. Ở đây minh họa cách Hash cực nhanh.
**Mã nguồn Java:**
```java
import java.util.*;

class Encrypter {
    Map<Character, String> encMap = new HashMap<>();
    Map<String, Integer> decryptCount = new HashMap<>();
    
    public Encrypter(char[] keys, String[] values, String[] dictionary) {
        for (int i = 0; i < keys.length; i++) {
            encMap.put(keys[i], values[i]);
        }
        for (String w : dictionary) {
            String enc = encrypt(w);
            if (enc != null) {
                decryptCount.put(enc, decryptCount.getOrDefault(enc, 0) + 1);
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
        return decryptCount.getOrDefault(word2, 0);
    }
}
```
**Độ phức tạp:** Khởi tạo $O(N \cdot L)$, Mã hóa $O(L)$, Giải mã $O(1)$.

---

## 27. Number of Matching Subsequences
**Đề bài chi tiết:** Cho chuỗi `S` và một danh sách `words`, đếm xem có bao nhiêu từ trong danh sách là chuỗi con (subsequence - không cần liên tiếp) của `S`.
**Phân tích thuật toán:** Bài này không dùng Aho-Corasick nguyên bản vì nó là *subsequence* chứ không phải *substring*. Tuy nhiên, có thể giải bằng một "Máy trạng thái" song song. Ta tạo các "bucket" chứa con trỏ trỏ tới từng từ bắt đầu bằng ký tự tương ứng. Khi duyệt `S`, ký tự nào thì ta tịnh tiến các con trỏ ở bucket đó lên một bước.
**Mã nguồn Java:**
```java
import java.util.*;

public class MatchingSubsequences {
    static class Node {
        String word;
        int index;
        public Node(String w, int i) {
            word = w;
            index = i;
        }
    }
    
    public int numMatchingSubseq(String s, String[] words) {
        List<Node>[] heads = new ArrayList[26];
        for (int i = 0; i < 26; i++) heads[i] = new ArrayList<>();
        
        for (String w : words) {
            heads[w.charAt(0) - 'a'].add(new Node(w, 0));
        }
        
        int ans = 0;
        for (char c : s.toCharArray()) {
            List<Node> oldBucket = heads[c - 'a'];
            heads[c - 'a'] = new ArrayList<>();
            
            for (Node node : oldBucket) {
                node.index++;
                if (node.index == node.word.length()) {
                    ans++;
                } else {
                    heads[node.word.charAt(node.index) - 'a'].add(node);
                }
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:** Thời gian $O(N + M)$ trong đó $N$ là len(S) và $M$ là tổng len(words). Không gian $O(K)$ con trỏ với $K$ là số từ.

---

## 28. Delete Subfolders from the Filesystem
**Đề bài chi tiết:** Cho một danh sách các thư mục, xóa tất cả các thư mục con (subfolders) trong danh sách đó và chỉ giữ lại thư mục gốc (parent folders).
**Phân tích thuật toán:** Sắp xếp từ điển (lexicographically). Khi đã sắp xếp, parent folder sẽ đứng ngay trước các subfolder của nó. Ta lưu folder hợp lệ gần nhất và kiểm tra nếu folder hiện tại bắt đầu bằng folder hợp lệ đó cộng thêm dấu `/` thì bỏ qua (nó là subfolder). 
**Mã nguồn Java:**
```java
import java.util.*;

public class DeleteSubfolders {
    public List<String> removeSubfolders(String[] folder) {
        Arrays.sort(folder);
        List<String> ans = new ArrayList<>();
        ans.add(folder[0]);
        
        for (int i = 1; i < folder.length; i++) {
            String lastFolder = ans.get(ans.size() - 1);
            if (!folder[i].startsWith(lastFolder + "/")) {
                ans.add(folder[i]);
            }
        }
        
        return ans;
    }
}
```
**Độ phức tạp:** Thời gian $O(N \log N \cdot L)$, Không gian $O(N \cdot L)$ cho việc sắp xếp mảng. (Cách Trie cũng mất $O(N \cdot L)$ nhưng thao tác tạo node chậm hơn sort mảng).

---

## 29. Find the Longest Substring Containing Vowels in Even Counts
**Đề bài chi tiết:** Tìm chuỗi con dài nhất mà trong đó mỗi nguyên âm (a, e, i, o, u) đều xuất hiện với số lần chẵn.
**Phân tích thuật toán:** Đây là ứng dụng của State Machine với Bitmask (rất giống tư duy FSM). Ta có 5 nguyên âm $\to$ 32 trạng thái chẵn lẻ. Nếu tại chỉ số $i$ trạng thái là $S$, và tại $j$ trạng thái cũng là $S$, thì chuỗi con từ $j+1 \dots i$ có nguyên âm chẵn. Dùng mảng lưu vị trí xuất hiện đầu tiên của mỗi trạng thái.
**Mã nguồn Java:**
```java
import java.util.*;

public class LongestSubstringEvenVowels {
    public int findTheLongestSubstring(String s) {
        int[] firstOccurrence = new int[32];
        Arrays.fill(firstOccurrence, -2);
        firstOccurrence[0] = -1; // State 0 (all even) at index -1
        
        int state = 0, maxLen = 0;
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == 'a') state ^= (1 << 0);
            else if (c == 'e') state ^= (1 << 1);
            else if (c == 'i') state ^= (1 << 2);
            else if (c == 'o') state ^= (1 << 3);
            else if (c == 'u') state ^= (1 << 4);
            
            if (firstOccurrence[state] != -2) {
                maxLen = Math.max(maxLen, i - firstOccurrence[state]);
            } else {
                firstOccurrence[state] = i;
            }
        }
        return maxLen;
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$ (vì mảng có kích thước 32 hằng số).

---

## 30. Wildcard Matching with Multiple Patterns
**Đề bài chi tiết:** Cho một đoạn văn bản và nhiều mẫu pattern có chứa ký tự `?` (khớp 1 ký tự bất kỳ) và `*` (khớp 1 chuỗi bất kỳ). Tìm xem các pattern nào khớp với toàn bộ hoặc một phần văn bản.
**Phân tích thuật toán:** Một bài mở rộng siêu phức tạp kết hợp Aho-Corasick, DP và NFA (Non-deterministic Finite Automaton). Ký tự `?` xử lý như transition với wildcard trong Aho-Corasick. Ký tự `*` tạo loop trong Automaton. Trong thực tế, các engine như RE2 (Regular Expression Engine) biên dịch chúng thành máy trạng thái FSM tương tự Aho-Corasick nhưng có epsilon transitions. Ở đây, ta dùng thuật toán DP 2D thông thường để minh họa cơ chế NFA cho **một** pattern.
**Mã nguồn Java:**
```java
public class WildcardMatching {
    // DP implementation simulating NFA states for a single pattern
    public boolean isMatch(String s, String p) {
        int sLen = s.length(), pLen = p.length();
        boolean[][] dp = new boolean[sLen + 1][pLen + 1];
        
        dp[0][0] = true;
        for (int j = 1; j <= pLen; j++) {
            if (p.charAt(j - 1) == '*') dp[0][j] = dp[0][j - 1];
        }
        
        for (int i = 1; i <= sLen; i++) {
            for (int j = 1; j <= pLen; j++) {
                if (p.charAt(j - 1) == '*') {
                    // Match empty string (*) or match one character
                    dp[i][j] = dp[i][j - 1] || dp[i - 1][j];
                } else if (p.charAt(j - 1) == '?' || s.charAt(i - 1) == p.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                }
            }
        }
        return dp[sLen][pLen];
    }
}
```
**Độ phức tạp:** DP tốn $O(N \cdot M)$. Với nhiều mẫu, tiếp cận Regex DFA sẽ giảm xuống $O(N)$ nhưng tốn chi phí xây dựng Exponential trong trường hợp tệ nhất.
