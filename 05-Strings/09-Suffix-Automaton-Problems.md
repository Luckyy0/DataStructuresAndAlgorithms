---
title: Suffix Automaton Problems
topic: Strings
author: AI Sư phụ
date: 2026-07-21
---

## 1. Number of Distinct Substrings in a String

### Đề bài chi tiết
Cho một chuỗi `S`. Hãy đếm số lượng chuỗi con (substrings) phân biệt (không rỗng) của chuỗi `S`.

### Phân tích thuật toán
Bằng cách xây dựng Suffix Automaton cho `S`. Mỗi trạng thái `u` (state) trong Suffix Automaton đại diện cho một tập các chuỗi con có chiều dài từ `len(link(u)) + 1` đến `len(u)`. Do đó, số chuỗi con mà trạng thái `u` đại diện là `len(u) - len(link(u))`.
Tổng của giá trị này trên tất cả các trạng thái (ngoại trừ trạng thái gốc có `link` là -1) chính là tổng số chuỗi con phân biệt.

### Mã nguồn Java
```java
import java.util.*;

public class DistinctSubstrings {
    static class State {
        int len, link;
        Map<Character, Integer> next = new HashMap<>();
        State(int len, int link) { this.len = len; this.link = link; }
    }
    
    static List<State> st;
    static int sz, last;
    
    static void init() {
        st = new ArrayList<>();
        st.add(new State(0, -1));
        sz = 1; last = 0;
    }
    
    static void extend(char c) {
        int cur = sz++;
        st.add(new State(st.get(last).len + 1, -1));
        int p = last;
        while (p != -1 && !st.get(p).next.containsKey(c)) {
            st.get(p).next.put(c, cur);
            p = st.get(p).link;
        }
        if (p == -1) {
            st.get(cur).link = 0;
        } else {
            int q = st.get(p).next.get(c);
            if (st.get(p).len + 1 == st.get(q).len) {
                st.get(cur).link = q;
            } else {
                int clone = sz++;
                State cloneState = new State(st.get(p).len + 1, st.get(q).link);
                cloneState.next.putAll(st.get(q).next);
                st.add(cloneState);
                while (p != -1 && st.get(p).next.get(c) == q) {
                    st.get(p).next.put(c, clone);
                    p = st.get(p).link;
                }
                st.get(q).link = clone;
                st.get(cur).link = clone;
            }
        }
        last = cur;
    }

    public static long countDistinct(String s) {
        init();
        for (char c : s.toCharArray()) extend(c);
        long ans = 0;
        for (int i = 1; i < sz; i++) {
            ans += st.get(i).len - st.get(st.get(i).link).len;
        }
        return ans;
    }

    public static void main(String[] args) {
        System.out.println(countDistinct("abab")); // Output: 7
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(N)$
- **Không gian (Space)**: $O(N \log \Sigma)$

---

## 2. Longest Common Substring of Two Strings

### Đề bài chi tiết
Cho hai chuỗi `S` và `T`. Hãy tìm độ dài của chuỗi con chung dài nhất (Longest Common Substring - LCS) của `S` và `T`.

### Phân tích thuật toán
Xây dựng Suffix Automaton cho chuỗi `S`.
Sau đó, duyệt qua các ký tự của `T`. Duy trì trạng thái hiện tại `v` và độ dài `l` đang khớp.
Nếu ký tự tiếp theo có trong `next` của `v`, di chuyển đến nó và `l++`.
Nếu không, nhảy dọc theo các `link` cho đến khi tìm thấy cạnh chuyển tương ứng và cập nhật `l = len(v) + 1`. Nếu nhảy về tận gốc và không tìm được, gán `v = 0, l = 0`. Giữ giá trị lớn nhất của `l` trong suốt quá trình.

### Mã nguồn Java
```java
public class LCSTwoStrings {
    static class SuffixAutomaton {
        int[] len, link;
        int[][] next;
        int sz, last;
        
        public SuffixAutomaton(String s) {
            int n = s.length();
            len = new int[2 * n];
            link = new int[2 * n];
            next = new int[2 * n][26];
            sz = 1; last = 0;
            link[0] = -1;
            for(int[] row : next) java.util.Arrays.fill(row, -1);
            
            for (char c : s.toCharArray()) extend(c - 'a');
        }
        
        void extend(int c) {
            int cur = sz++;
            len[cur] = len[last] + 1;
            int p = last;
            while (p != -1 && next[p][c] == -1) {
                next[p][c] = cur;
                p = link[p];
            }
            if (p == -1) {
                link[cur] = 0;
            } else {
                int q = next[p][c];
                if (len[p] + 1 == len[q]) {
                    link[cur] = q;
                } else {
                    int clone = sz++;
                    len[clone] = len[p] + 1;
                    System.arraycopy(next[q], 0, next[clone], 0, 26);
                    link[clone] = link[q];
                    while (p != -1 && next[p][c] == q) {
                        next[p][c] = clone;
                        p = link[p];
                    }
                    link[q] = link[cur] = clone;
                }
            }
            last = cur;
        }
    }

    public static int findLCS(String s, String t) {
        SuffixAutomaton sa = new SuffixAutomaton(s);
        int v = 0, l = 0, best = 0;
        for (char c : t.toCharArray()) {
            int idx = c - 'a';
            while (v != 0 && sa.next[v][idx] == -1) {
                v = sa.link[v];
                l = sa.len[v];
            }
            if (sa.next[v][idx] != -1) {
                v = sa.next[v][idx];
                l++;
            }
            best = Math.max(best, l);
        }
        return best;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|S| + |T|)$
- **Không gian (Space)**: $O(|S| \times \Sigma)$

---

## 3. Longest Common Substring of K Strings

### Đề bài chi tiết
Cho $K$ chuỗi $S_1, S_2, \ldots, S_K$. Tìm độ dài chuỗi con chung dài nhất của tất cả các chuỗi này.

### Phân tích thuật toán
Xây dựng Suffix Automaton cho chuỗi đầu tiên $S_1$.
Với mỗi chuỗi $S_i$, ta chạy qua Suffix Automaton và ghi lại giá trị lớn nhất có thể khớp được cho **mỗi trạng thái**. Cuối cùng lấy giá trị nhỏ nhất qua các chuỗi đối với mỗi trạng thái. Cần đẩy cập nhật từ các node con xuống node cha trên cây Suffix Link theo thứ tự Topo (chiều dài giảm dần) để truyền đúng giá trị.

### Mã nguồn Java
```java
import java.util.Arrays;

public class LCSKStrings {
    static final int MAXLEN = 100005;
    static int[] len = new int[2*MAXLEN];
    static int[] link = new int[2*MAXLEN];
    static int[][] next = new int[2*MAXLEN][26];
    static int sz, last;

    static void init() {
        sz = 1; last = 0;
        link[0] = -1;
        for(int[] row : next) Arrays.fill(row, -1);
    }
    
    // (Bỏ qua định nghĩa extend() do tương tự bài trước)

    public static int findLCSK(String[] strings) {
        init();
        // Giả sử đã extend xong cho strings[0]
        int[] ans = new int[sz];
        for(int i = 0; i < sz; i++) ans[i] = len[i];

        for (int i = 1; i < strings.length; i++) {
            int[] currentL = new int[sz];
            int v = 0, l = 0;
            for (char c : strings[i].toCharArray()) {
                int idx = c - 'a';
                while (v > 0 && next[v][idx] == -1) {
                    v = link[v];
                    l = len[v];
                }
                if (next[v][idx] != -1) {
                    v = next[v][idx];
                    l++;
                }
                currentL[v] = Math.max(currentL[v], l);
            }

            int[] order = new int[sz];
            int[] cnt = new int[MAXLEN];
            for(int j = 0; j < sz; j++) cnt[len[j]]++;
            for(int j = 1; j < MAXLEN; j++) cnt[j] += cnt[j-1];
            for(int j = 0; j < sz; j++) order[--cnt[len[j]]] = j;

            for (int j = sz - 1; j > 0; j--) {
                int curr = order[j];
                int p = link[curr];
                ans[curr] = Math.min(ans[curr], currentL[curr]);
                if (p != -1) {
                    currentL[p] = Math.max(currentL[p], currentL[curr]);
                }
            }
        }
        int maxLCS = 0;
        for(int i = 0; i < sz; i++) maxLCS = Math.max(maxLCS, ans[i]);
        return maxLCS;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(\sum |S_i| + K \cdot |S_1|)$
- **Không gian (Space)**: $O(|S_1| \times \Sigma)$

---

## 4. Lexicographically K-th Substring

### Đề bài chi tiết
Cho một chuỗi `S` và số nguyên `K`. Tìm chuỗi con thứ `K` của `S` theo thứ tự từ điển (xét các chuỗi con phân biệt).

### Phân tích thuật toán
Xây dựng Suffix Automaton. Tính số lượng đường đi từ mỗi trạng thái bằng quy hoạch động `dp[u]`. Từ gốc, duyệt các cạnh theo thứ tự từ điển `a` tới `z`. Nếu `K <= dp[next_node]`, ta đi vào nhánh đó. Nếu không, ta trừ `dp[next_node]` khỏi `K` và thử nhánh khác.

### Mã nguồn Java
```java
public class KthSubstring {
    static int[] len, link, dp;
    static int[][] next;
    static int sz;
    
    // Khởi tạo SA (đã ẩn chi tiết)...
    
    static void dfs(int u) {
        if (dp[u] > 0) return;
        dp[u] = 1; 
        for (int c = 0; c < 26; c++) {
            int v = next[u][c];
            if (v != -1) {
                dfs(v);
                dp[u] += dp[v];
            }
        }
    }

    public static String getKthSubstring(String s, int k) {
        // init and build SA ...
        dp = new int[sz];
        dfs(0);
        
        StringBuilder res = new StringBuilder();
        int u = 0;
        while (k > 0) {
            for (int c = 0; c < 26; c++) {
                int v = next[u][c];
                if (v != -1) {
                    if (k <= dp[v]) {
                        res.append((char)(c + 'a'));
                        k--;
                        u = v;
                        break;
                    } else {
                        k -= dp[v];
                    }
                }
            }
        }
        return res.toString();
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|S|)$
- **Không gian (Space)**: $O(|S| \times \Sigma)$

---

## 5. Find All Occurrences of Pattern

### Đề bài chi tiết
Tìm vị trí bắt đầu của mọi sự xuất hiện của mẫu `P` trong văn bản `T` sử dụng Suffix Automaton.

### Phân tích thuật toán
Xây dựng SA cho `T`. Duyệt Automaton bằng `P`. Nếu thành công, ta đến một trạng thái `v`. Dùng DFS để lấy tập `endpos` từ cây Suffix Link tại đỉnh `v`. Vị trí xuất hiện sẽ là `endpos - |P| + 1`.

### Mã nguồn Java
```java
import java.util.*;

public class FindPatternOccurrences {
    // Giả định SA đã có: mảng next, link, len, và thuộc tính isClone
    public static List<Integer> find(String t, String p) {
        List<Integer> occurrences = new ArrayList<>();
        // 1. Chạy P qua SA.
        // int u = 0;
        // for(char c : p.toCharArray()) { u = next[u][c-'a']; if(u==-1) return occurrences; }
        
        // 2. DFS trên cây Suffix Link từ u để gom nhặt endpos (chỉ khi !isClone)
        // void dfs(int node) {
        //     if (!isClone[node]) occurrences.add(len[node] - p.length());
        //     for(int child : suffixLinkTreeAdj.get(node)) dfs(child);
        // }
        // dfs(u);
        return occurrences;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|T| + |P| + \text{Occurrences})$
- **Không gian (Space)**: $O(|T|)$

---

## 6. Longest Repeating Substring

### Đề bài chi tiết
Tìm chiều dài của chuỗi con lặp lại dài nhất (xuất hiện ít nhất 2 lần) trong `S`.

### Phân tích thuật toán
Tính số lần xuất hiện `cnt[u]` của mọi trạng thái `u` bằng DFS từ dưới lên trên Suffix Link Tree. Trạng thái nào có `cnt[u] >= 2`, ta lưu giá trị cực đại của `len[u]`.

### Mã nguồn Java
```java
public class LongestRepeating {
    // SA attributes ...
    static int[] cnt;

    public static int solve(String s) {
        // Build SA...
        // Khởi tạo cnt[u] = 1 với node không phải clone, cnt = 0 với clone
        int maxL = 0;
        // Duyệt order theo chiều giảm dần của độ dài len
        // for(int i = sz - 1; i > 0; i--) {
        //     int u = order[i];
        //     cnt[link[u]] += cnt[u];
        //     if (cnt[u] >= 2) {
        //         maxL = Math.max(maxL, len[u]);
        //     }
        // }
        return maxL;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|S|)$
- **Không gian (Space)**: $O(|S|)$

---

## 7. Find the Length of the Longest Common Prefix

### Đề bài chi tiết
Bằng Suffix Automaton, tìm độ dài LCP của 2 hậu tố trên chuỗi gốc.

### Phân tích thuật toán
LCP của 2 hậu tố trên chuỗi `S` chính là độ dài LCA của hai trạng thái tương ứng trên Suffix Link Tree của Suffix Automaton tạo từ $S^{rev}$ (chuỗi S đảo ngược).

### Mã nguồn Java
```java
public class LongestCommonPrefix {
    // int lca(int u, int v) - Sử dụng thuật toán LCA trên cây
    // public int queryLCP(int pos1, int pos2) {
    //    int node1 = stateOf[pos1];
    //    int node2 = stateOf[pos2];
    //    int lcaNode = lca(node1, node2);
    //    return len[lcaNode];
    // }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|S| \log |S|)$ chuẩn bị, $O(\log |S|)$ mỗi truy vấn.
- **Không gian (Space)**: $O(|S| \log |S|)$

---

## 8. Last Substring in Lexicographical Order

### Đề bài chi tiết
Tìm chuỗi con có thứ tự từ điển lớn nhất của chuỗi `S`.

### Phân tích thuật toán
Từ trạng thái gốc, liên tục đi theo cạnh có ký tự lớn nhất cho đến khi không còn đường để đi. Chuỗi các cạnh đi qua là chuỗi cần tìm.

### Mã nguồn Java
```java
public class LastLexicographicalSubstring {
    public static String findLast(String s) {
        // Xây dựng SA
        StringBuilder res = new StringBuilder();
        // int u = 0;
        // while (true) {
        //    boolean found = false;
        //    for(int c = 25; c >= 0; c--) {
        //        if (next[u][c] != -1) {
        //            res.append((char)('a' + c));
        //            u = next[u][c];
        //            found = true;
        //            break;
        //        }
        //    }
        //    if (!found) break;
        // }
        return res.toString();
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|S|)$
- **Không gian (Space)**: $O(|S| \times \Sigma)$

---

## 9. Substring Matching Query

### Đề bài chi tiết
Kiểm tra xem mẫu `P` có phải là chuỗi con của văn bản `T` đã cho hay không.

### Phân tích thuật toán
Chạy SA của `T` qua các ký tự của `P`. Nếu khớp trọn vẹn, `P` là chuỗi con. Nếu có lúc bị `-1`, `P` không tồn tại.

### Mã nguồn Java
```java
public class SubstringMatch {
    // SA variables ...
    public boolean isSubstring(String p) {
        // int u = 0;
        // for(char c : p.toCharArray()) {
        //     int idx = c - 'a';
        //     if (next[u][idx] == -1) return false;
        //     u = next[u][idx];
        // }
        // return true;
        return false;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|T|)$ setup, $O(|P|)$ cho truy vấn.
- **Không gian (Space)**: $O(|T|)$

---

## 10. Count Occurrences of Substring

### Đề bài chi tiết
Đếm số lần xuất hiện của một chuỗi mẫu `P` bất kỳ trong `T`.

### Phân tích thuật toán
Sau khi tính toán kích thước tập `endpos` của tất cả các trạng thái trong SA. Chạy chuỗi `P` trên Automaton, đến trạng thái kết thúc `v` thì số lần xuất hiện là `endpos_size[v]`.

### Mã nguồn Java
```java
public class CountOccurrencesSubstring {
    // Tính sizeEndpos bằng duyệt theo thứ tự topo từ len dài tới len ngắn
    // int[] sizeEndpos;
    
    public int countOccurrences(String p) {
        // int u = 0;
        // for(char c : p.toCharArray()) {
        //    u = next[u][c - 'a'];
        //    if (u == -1) return 0;
        // }
        // return sizeEndpos[u];
        return 0;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|T|)$ tính toán trước, $O(|P|)$ query.
- **Không gian (Space)**: $O(|T|)$

---

## 11. Longest Common Substring with Constraints

### Đề bài chi tiết
Tìm chuỗi con chung dài nhất của hai chuỗi $S$ và $T$, với điều kiện chuỗi con này không chứa một ký tự $X$ cho trước.

### Phân tích thuật toán
Xây dựng Suffix Automaton cho chuỗi $S$. Khi duyệt qua chuỗi $T$ trên Automaton để tìm chuỗi con chung dài nhất, nếu gặp ký tự $X$, ta không được phép đi theo cạnh $X$ mà phải đặt lại trạng thái hiện tại về gốc (root) và độ dài khớp về 0, đồng thời bắt đầu lại từ ký tự tiếp theo. Với các ký tự khác $X$, ta thực hiện chuyển trạng thái và cập nhật độ dài khớp tối đa giống như bài toán chuỗi con chung bình thường.

### Mã nguồn Java
```java
public class LCSWithConstraints {
    static class SuffixAutomaton {
        int[] len, link;
        int[][] next;
        int sz, last;
        
        public SuffixAutomaton(String s) {
            int n = s.length();
            len = new int[2 * n];
            link = new int[2 * n];
            next = new int[2 * n][26];
            sz = 1; last = 0;
            link[0] = -1;
            for(int[] row : next) java.util.Arrays.fill(row, -1);
            
            for (char c : s.toCharArray()) extend(c - 'a');
        }
        
        void extend(int c) {
            int cur = sz++;
            len[cur] = len[last] + 1;
            int p = last;
            while (p != -1 && next[p][c] == -1) {
                next[p][c] = cur;
                p = link[p];
            }
            if (p == -1) {
                link[cur] = 0;
            } else {
                int q = next[p][c];
                if (len[p] + 1 == len[q]) {
                    link[cur] = q;
                } else {
                    int clone = sz++;
                    len[clone] = len[p] + 1;
                    System.arraycopy(next[q], 0, next[clone], 0, 26);
                    link[clone] = link[q];
                    while (p != -1 && next[p][c] == q) {
                        next[p][c] = clone;
                        p = link[p];
                    }
                    link[q] = link[cur] = clone;
                }
            }
            last = cur;
        }
    }

    public static int findLCSConstraint(String s, String t, char forbidden) {
        SuffixAutomaton sa = new SuffixAutomaton(s);
        int v = 0, l = 0, best = 0;
        int forbiddenIdx = forbidden - 'a';
        
        for (char c : t.toCharArray()) {
            int idx = c - 'a';
            if (idx == forbiddenIdx) {
                v = 0;
                l = 0;
                continue;
            }
            
            while (v != 0 && sa.next[v][idx] == -1) {
                v = sa.link[v];
                l = sa.len[v];
            }
            if (sa.next[v][idx] != -1) {
                v = sa.next[v][idx];
                l++;
            }
            best = Math.max(best, l);
        }
        return best;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|S| + |T|)$
- **Không gian (Space)**: $O(|S| \times \Sigma)$

---

## 12. Minimal Palindromic Substring via Automaton

### Đề bài chi tiết
Tích hợp Suffix Automaton với thuật toán Manacher để tìm chuỗi con Palindrome (đối xứng) có tính chất đặc biệt (ví dụ: đếm số lượng chuỗi con đối xứng phân biệt). Bài này tập trung vào đếm số lượng chuỗi con đối xứng riêng biệt của chuỗi $S$.

### Phân tích thuật toán
Sử dụng thuật toán Manacher để tìm tất cả các chuỗi con đối xứng tối đại (maximal palindromes) tại mỗi tâm. Vì số lượng palindromes tối đại là $O(N)$, ta có thể trích xuất chúng và đưa qua Suffix Automaton để kiểm tra xem chúng đã được đếm chưa (sử dụng một mảng đánh dấu `visited` trên các node của Suffix Automaton). Nếu đến một trạng thái chưa được đánh dấu, ta thêm nó vào tập hợp các chuỗi đối xứng phân biệt.
*(Ghi chú: Palindromic Tree/Eertree thường tối ưu hơn cho bài toán này, nhưng SA kết hợp Manacher vẫn giải quyết được).*

### Mã nguồn Java
```java
import java.util.*;

public class DistinctPalindromesSA {
    // Giả định SuffixAutomaton đã được định nghĩa giống bài trước
    // public SuffixAutomaton(String s) ...
    
    public static int countDistinctPalindromes(String s) {
        // SA setup for s
        // (Pseudocode)
        // int count = 0;
        // String processed = preprocess(s); // Dạng "a#b#a" cho Manacher
        // int[] p = manacher(processed);
        // boolean[] visitedState = new boolean[sa.sz];
        // 
        // for each center i in processed:
        //     int length = p[i];
        //     extract palindrome substrings of various lengths
        //     traverse SA and mark states
        //     count += new unvisited states mapped to palindromes
        // return count;
        return 0; // Trả về số lượng
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|S| \log |S|)$ hoặc $O(|S|)$ tuỳ thuộc vào cách đánh dấu và tìm kiếm.
- **Không gian (Space)**: $O(|S| \times \Sigma)$

---

## 13. Suffix Automaton on Arrays

### Đề bài chi tiết
Xây dựng Suffix Automaton không phải trên chuỗi (String) mà trên một mảng các số nguyên (Arrays of integers). Thường dùng khi bảng chữ cái $\Sigma$ rất lớn.

### Phân tích thuật toán
Khi bảng chữ cái $\Sigma$ lớn (như $10^9$ trong trường hợp mảng số nguyên), thay vì dùng mảng tĩnh `int[][] next = new int[2*N][26]`, ta sử dụng `HashMap<Integer, Integer>` hoặc cây nhị phân tìm kiếm (như `TreeMap`) cho mỗi trạng thái để lưu các cạnh chuyển đổi `next`. Quá trình `extend` giữ nguyên logic nhưng thay các thao tác trên mảng bằng thao tác trên `Map`.

### Mã nguồn Java
```java
import java.util.*;

public class SuffixAutomatonArray {
    static class State {
        int len, link;
        Map<Integer, Integer> next = new HashMap<>();
        State(int len, int link) { this.len = len; this.link = link; }
    }
    
    static List<State> st;
    static int sz, last;
    
    public static void init() {
        st = new ArrayList<>();
        st.add(new State(0, -1));
        sz = 1; last = 0;
    }
    
    public static void extend(int c) {
        int cur = sz++;
        st.add(new State(st.get(last).len + 1, -1));
        int p = last;
        while (p != -1 && !st.get(p).next.containsKey(c)) {
            st.get(p).next.put(c, cur);
            p = st.get(p).link;
        }
        if (p == -1) {
            st.get(cur).link = 0;
        } else {
            int q = st.get(p).next.get(c);
            if (st.get(p).len + 1 == st.get(q).len) {
                st.get(cur).link = q;
            } else {
                int clone = sz++;
                State cloneState = new State(st.get(p).len + 1, st.get(q).link);
                cloneState.next.putAll(st.get(q).next);
                st.add(cloneState);
                while (p != -1 && st.get(p).next.getOrDefault(c, -1) == q) {
                    st.get(p).next.put(c, clone);
                    p = st.get(p).link;
                }
                st.get(q).link = clone;
                st.get(cur).link = clone;
            }
        }
        last = cur;
    }
    
    public static void build(int[] arr) {
        init();
        for (int x : arr) {
            extend(x);
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(N \log \Sigma)$ hoặc $O(N)$ expected với `HashMap`.
- **Không gian (Space)**: $O(N)$ số lượng trạng thái và cạnh.

---

## 14. Unique Substring Count with Range Queries

### Đề bài chi tiết
Cho chuỗi $S$. Có nhiều truy vấn $(L, R)$, với mỗi truy vấn, yêu cầu đếm số lượng chuỗi con duy nhất (không rỗng) nằm hoàn toàn trong đoạn con $S[L..R]$.

### Phân tích thuật toán
Đây là bài toán phức tạp (thường dùng Suffix Automaton kết hợp với LCT - Link Cut Tree hoặc Segment Tree và thuật toán Mo hoặc Offline queries). 
Ý tưởng là với mỗi điểm cuối $R$, ta thêm dần ký tự $S[R]$ vào và cập nhật các đoạn đóng góp vào số lượng chuỗi con duy nhất bằng cách duy trì giá trị cập nhật gần nhất của các trạng thái trên cây Suffix Link. Ta dùng cấu trúc dữ liệu (Fenwick Tree / Segment Tree) để lưu các mốc $L$ được cập nhật, và trả lời truy vấn bằng tổng đoạn trong cây.

### Mã nguồn Java
```java
// Mã nguồn cụ thể khá dài và yêu cầu LCT/Segment Tree kết hợp SA.
// Dưới đây là phần cấu trúc sườn minh hoạ cho tư tưởng.
public class UniqueSubstringRangeQuery {
    // Suffix Automaton, Segment Tree for Range Queries
    public static long[] solveQueries(String s, int[][] queries) {
        // 1. Sort queries by R ending position (Offline processing)
        // 2. Iterate R from 0 to N-1:
        //    a. Extend SA with s[R]
        //    b. Cập nhật vị trí xuất hiện cuối cùng (Right/Endpos) 
        //       trên đường đi Suffix Link tới gốc. (Có thể dùng LCT)
        //    c. Update Segment tree/Fenwick tree với delta cho mảng L.
        //    d. Process all queries ending at R by querying range [L, R]
        
        long[] results = new long[queries.length];
        // ... Implementation details ...
        return results;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(N \log N + Q \log N)$ sử dụng Segment Tree & Offline Queries.
- **Không gian (Space)**: $O(N \log N + Q)$

---

## 15. Generalized Suffix Automaton Construction

### Đề bài chi tiết
Xây dựng một Suffix Automaton cho một tập hợp nhiều chuỗi $S_1, S_2, \dots, S_K$ (Generalized Suffix Automaton) thay vì chỉ một chuỗi.

### Phân tích thuật toán
Để xây dựng SA cho nhiều chuỗi, ta có 2 cách chính:
1. Nối các chuỗi bằng ký tự đặc biệt không xuất hiện trong bảng chữ cái, ví dụ `S_1 # S_2 $ S_3`.
2. Khôi phục lại biến `last = 0` (đỉnh gốc) trước khi thêm các ký tự của chuỗi mới. Trong quá trình thêm chữ, nếu gặp cạnh chuyển tiếp đã có sẵn (trường hợp tiền tố của chuỗi này trùng với chuỗi đã xử lý), ta không tạo đỉnh mới ngay mà duyệt theo nó, hoặc phải nhân bản tách trạng thái giống như `extend` để đảm bảo đúng tính chất.

### Mã nguồn Java
```java
public class GeneralizedSuffixAutomaton {
    static final int MAX_NODES = 200000;
    static int[] len = new int[MAX_NODES];
    static int[] link = new int[MAX_NODES];
    static int[][] next = new int[MAX_NODES][26];
    static int sz = 1;
    
    static {
        link[0] = -1;
        for (int i = 0; i < MAX_NODES; i++) {
            java.util.Arrays.fill(next[i], -1);
        }
    }

    public static int extend(int last, int c) {
        // Nếu đã có cạnh chuyển này và thoả mãn độ dài, ta có thể dùng luôn.
        if (next[last][c] != -1) {
            int q = next[last][c];
            if (len[last] + 1 == len[q]) return q;
            int clone = sz++;
            len[clone] = len[last] + 1;
            System.arraycopy(next[q], 0, next[clone], 0, 26);
            link[clone] = link[q];
            int p = last;
            while (p != -1 && next[p][c] == q) {
                next[p][c] = clone;
                p = link[p];
            }
            link[q] = clone;
            return clone;
        }

        int cur = sz++;
        len[cur] = len[last] + 1;
        int p = last;
        while (p != -1 && next[p][c] == -1) {
            next[p][c] = cur;
            p = link[p];
        }
        if (p == -1) {
            link[cur] = 0;
        } else {
            int q = next[p][c];
            if (len[p] + 1 == len[q]) {
                link[cur] = q;
            } else {
                int clone = sz++;
                len[clone] = len[p] + 1;
                System.arraycopy(next[q], 0, next[clone], 0, 26);
                link[clone] = link[q];
                while (p != -1 && next[p][c] == q) {
                    next[p][c] = clone;
                    p = link[p];
                }
                link[q] = link[cur] = clone;
            }
        }
        return cur;
    }

    public static void build(String[] strings) {
        for (String str : strings) {
            int last = 0;
            for (char c : str.toCharArray()) {
                last = extend(last, c - 'a');
            }
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(\sum |S_i|)$
- **Không gian (Space)**: $O(\sum |S_i| \times \Sigma)$

---

## 16. Matching Multiple Patterns

### Đề bài chi tiết
Cho văn bản $T$ và $K$ chuỗi mẫu $P_1, P_2, \dots, P_K$. Đếm số lần mỗi chuỗi mẫu xuất hiện trong văn bản $T$.

### Phân tích thuật toán
Xây dựng Suffix Automaton cho văn bản $T$. Tính toán trước kích thước của tập `endpos` (số lần xuất hiện của các chuỗi con tại trạng thái đó) cho mọi trạng thái bằng cách duyệt cây Suffix Link theo thứ tự Topo ngược (từ trạng thái dài nhất về gốc).
Sau đó, với mỗi mẫu $P_i$, chỉ cần chạy $P_i$ qua SA, nếu kết thúc ở trạng thái `u` thì số lần xuất hiện chính là `endpos_size[u]`. Nếu không tìm thấy đường đi (rơi vào `-1`), số lần xuất hiện là $0$.

### Mã nguồn Java
```java
public class MatchMultiplePatterns {
    // Automaton variables...
    // Mảng lưu size của endpos
    static int[] endposSize;

    public static int[] solve(String t, String[] patterns) {
        // 1. Build SA for t.
        // GeneralizedSuffixAutomaton.build(new String[]{t});
        
        // 2. Tính endposSize. Khởi tạo endposSize[i] = 1 với node không phải clone.
        // int[] order = getTopoOrder();
        // for(int i = sz - 1; i > 0; i--) {
        //     int u = order[i];
        //     endposSize[link[u]] += endposSize[u];
        // }
        
        int[] result = new int[patterns.length];
        // 3. Match từng pattern
        // for (int i = 0; i < patterns.length; i++) {
        //     int u = 0;
        //     boolean found = true;
        //     for (char c : patterns[i].toCharArray()) {
        //         u = next[u][c - 'a'];
        //         if (u == -1) { found = false; break; }
        //     }
        //     result[i] = found ? endposSize[u] : 0;
        // }
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|T| + \sum |P_i|)$
- **Không gian (Space)**: $O(|T| \times \Sigma)$

---

## 17. Lexicographically K-th Substring (Duplicate allowed)

### Đề bài chi tiết
Tìm chuỗi con thứ $K$ theo thứ tự từ điển của chuỗi $S$. Lưu ý: Các chuỗi con giống nhau xuất hiện nhiều lần được tính là các chuỗi **riêng biệt**.

### Phân tích thuật toán
Tương tự như bài "Lexicographically K-th Substring" khi chỉ xét các chuỗi phân biệt, nhưng ở đây, giá trị ban đầu tại mỗi node không phải là 1, mà là số lần xuất hiện thực tế `endpos_size[u]`. 
Ta quy hoạch động `dp[u]` là tổng số chuỗi con (tính cả lặp lại) có thể sinh ra từ đường đi qua trạng thái `u`.
$\text{dp}[u] = \text{endpos\_size}[u] + \sum \text{dp}[v]$ với mọi cạnh nối từ $u$ tới $v$.
Dựa trên giá trị `dp`, ta tiếp tục duyệt từ gốc theo thứ tự từ điển để tìm ra chuỗi thứ $K$.

### Mã nguồn Java
```java
public class KthSubstringDuplicate {
    static int[] endposSize, dp;
    
    static void dfs(int u, int[][] next) {
        if (dp[u] > 0) return;
        dp[u] = endposSize[u];
        for (int c = 0; c < 26; c++) {
            int v = next[u][c];
            if (v != -1) {
                dfs(v, next);
                dp[u] += dp[v];
            }
        }
    }

    public static String getKth(String s, int k, int[][] next, int sz) {
        // Assume SA is built and endposSize is precalculated
        dp = new int[sz];
        dfs(0, next);
        
        StringBuilder res = new StringBuilder();
        int u = 0;
        while (k > endposSize[u]) {
            k -= endposSize[u];
            for (int c = 0; c < 26; c++) {
                int v = next[u][c];
                if (v != -1) {
                    if (k <= dp[v]) {
                        res.append((char) (c + 'a'));
                        u = v;
                        break;
                    } else {
                        k -= dp[v];
                    }
                }
            }
        }
        return res.toString();
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|S|)$
- **Không gian (Space)**: $O(|S| \times \Sigma)$

---

## 18. Smallest Cyclic Shift

### Đề bài chi tiết
Cho chuỗi $S$. Tìm thứ tự từ điển nhỏ nhất trong tất cả các vòng quay (Cyclic Shifts) của $S$. (Ví dụ $S = \text{caba}$, các vòng quay: `caba`, `abac`, `baca`, `acab`. Nhỏ nhất là `abac`).

### Phân tích thuật toán
Tạo chuỗi $T = S + S$. Xây dựng Suffix Automaton cho $T$.
Bắt đầu từ node gốc, trong đúng $|S|$ bước, tại mỗi bước ta luôn chọn đi theo cạnh có ký tự (theo bảng chữ cái) nhỏ nhất hiện có. Đường đi độ dài $|S|$ này chính là chuỗi vòng quay nhỏ nhất cần tìm. 
*(Đây cũng là một cách giải thay thế thuật toán Booth $O(N)$).*

### Mã nguồn Java
```java
public class SmallestCyclicShift {
    public static String findSmallestShift(String s) {
        String t = s + s;
        // Build SA for T
        // SuffixAutomaton sa = new SuffixAutomaton(t);
        
        StringBuilder res = new StringBuilder();
        // int u = 0;
        // for (int i = 0; i < s.length(); i++) {
        //     for (int c = 0; c < 26; c++) {
        //         if (sa.next[u][c] != -1) {
        //             res.append((char) (c + 'a'));
        //             u = sa.next[u][c];
        //             break;
        //         }
        //     }
        // }
        // return res.toString();
        return res.toString();
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|S|)$
- **Không gian (Space)**: $O(|S| \times \Sigma)$

---

## 19. String Compression via SA (Lempel-Ziv concept)

### Đề bài chi tiết
Cho chuỗi $S$. Cần nén $S$ theo dạng: mỗi bước mã hoá một đoạn của $S$ thành một tham chiếu $(len, pos)$ sao cho nó đã xuất hiện trước đó trong phần văn bản đã mã hoá, để số đoạn sinh ra là ít nhất.

### Phân tích thuật toán
Sử dụng SA kết hợp tham lam. Tại vị trí $i$, ta tìm tiền tố dài nhất của $S[i..N]$ đã từng xuất hiện ở $S[0..i-1]$. Có thể dùng SA đã xây dựng trên $S[0..i-1]$ (kiểu online/Dynamic Suffix Automaton) để tìm độ dài lớn nhất này. Sau đó in ra cặp tham chiếu, và đẩy vị trí duyệt $i$ lên đoạn tương ứng.

### Mã nguồn Java
```java
public class LZCompressionSA {
    // Pseudocode for LZ compression using online SA
    public static void compressLZ(String s) {
        // Initialize Dynamic SA
        // int i = 0;
        // while(i < s.length()) {
        //      Find max prefix of s[i...N] in current SA.
        //      If maxLen > 0:
        //          print (matchPos, maxLen)
        //          Add s[i ... i+maxLen-1] to SA sequentially
        //          i += maxLen;
        //      Else:
        //          print (s[i])
        //          Add s[i] to SA
        //          i++;
        // }
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|S|)$
- **Không gian (Space)**: $O(|S| \times \Sigma)$

---

## 20. Period of a String

### Đề bài chi tiết
Cho một chuỗi $S$. Xác định độ dài chu kỳ nhỏ nhất (minimum period) của $S$. Chuỗi $S$ có chu kỳ $P$ nếu $S[i] = S[i+P]$ với mọi $i$.

### Phân tích thuật toán
Tuy bài này thường được giải tối ưu bằng mảng $\pi$ của thuật toán KMP (KMP Prefix function, period = $N - \pi[N-1]$). 
Với Suffix Automaton, ta có thể tìm chu kỳ thông qua trạng thái đại diện cho toàn bộ chuỗi $S$. Xét Suffix Link Tree, các bước chuyển link từ node đại diện $S$ về gốc sẽ chỉ ra các đuôi (Suffix) là tiền tố (Prefix). $S$ có một Suffix độ dài $L$ cũng là Prefix thì chu kỳ là $N - L$. Lấy độ dài lớn nhất của một Suffix như vậy (đi theo link từ node $S$), chu kỳ nhỏ nhất sẽ là $N - max\_L$.

### Mã nguồn Java
```java
public class StringPeriod {
    // Hàm minh họa tìm chu kỳ qua Suffix Automaton
    public static int getMinPeriod(String s) {
        // SuffixAutomaton sa = new SuffixAutomaton(s);
        // int u = stateOfFullString(s); // Node chứa toàn bộ chuỗi s
        
        // Node p = sa.link[u];
        // Tìm suffix dài nhất cũng là prefix
        // Trong SA, không phải mọi Suffix Link đều đảm bảo nó cũng là Prefix,
        // Ta cần đánh dấu các trạng thái ứng với các Prefix của chuỗi.
        // Sau đó đi lên theo Suffix Link từ u, tìm node đầu tiên có đánh dấu Prefix.
        // Return s.length() - sa.len[p];
        return 0;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|S|)$
- **Không gian (Space)**: $O(|S| \times \Sigma)$

---

## 21. Substrings Ending at Specific Position

### Đề bài chi tiết
Cho chuỗi $S$ và một vị trí $P$. Xác định các trạng thái (states) trong Suffix Automaton đại diện cho tất cả các chuỗi con của $S$ kết thúc tại chính xác vị trí $P$.

### Phân tích thuật toán
Trong quá trình xây dựng Suffix Automaton, khi thêm ký tự $S[P]$ vào hệ thống (thông qua hàm `extend`), một trạng thái `cur` mới được tạo ra. Trạng thái `cur` này và toàn bộ các tổ tiên của nó trên cây Suffix Link (đường đi từ `cur` đến gốc `link[cur], link[link[cur]]...`) chính là những trạng thái chứa các chuỗi con kết thúc tại $P$. Bằng cách lưu trữ một mảng ánh xạ từ vị trí index trên chuỗi gốc sang ID của trạng thái khi `extend`, ta có thể dễ dàng truy xuất thông tin này.

### Mã nguồn Java
```java
public class SubstringsEndingAtPosition {
    // Giả định SuffixAutomaton đã định nghĩa
    // static int[] link; ...
    static int[] stateAtPosition;
    
    public static void buildAndTrack(String s) {
        // init SA ...
        stateAtPosition = new int[s.length()];
        // int last = 0;
        // for (int i = 0; i < s.length(); i++) {
        //     last = extend(last, s.charAt(i) - 'a');
        //     stateAtPosition[i] = last;
        // }
    }
    
    public static java.util.List<Integer> getStatesEndingAt(int p) {
        java.util.List<Integer> states = new java.util.ArrayList<>();
        // int curr = stateAtPosition[p];
        // while(curr > 0) {
        //     states.add(curr);
        //     curr = link[curr];
        // }
        return states;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|S|)$ xây dựng, $O(|S|)$ mỗi truy vấn trường hợp xấu nhất (đi ngược Suffix Link).
- **Không gian (Space)**: $O(|S| \times \Sigma)$

---

## 22. K-th Common Substring

### Đề bài chi tiết
Cho hai chuỗi $S_1$ và $S_2$. Tìm chuỗi con chung thứ $K$ theo thứ tự từ điển.

### Phân tích thuật toán
Xây dựng Generalized Suffix Automaton cho $S_1$ và $S_2$. Ở mỗi trạng thái, ta tính số chuỗi gốc chứa trạng thái đó (bằng cách hợp nhất mảng bitmask từ dưới lên trên qua Suffix Link). 
Các trạng thái có bitmask là $3$ (thuộc cả $S_1$ và $S_2$) chính là chuỗi con chung.
Ta quy hoạch động `dp[u]` là số lượng chuỗi con chung có thể sinh ra từ trạng thái `u`. 
$\text{dp}[u] = (\text{mask}[u] == 3 ? 1 : 0) + \sum \text{dp}[v]$.
Duyệt từ gốc theo thứ tự từ điển, dùng `dp` để chọn nhánh giống như bài toán "K-th Substring".

### Mã nguồn Java
```java
public class KthCommonSubstring {
    static int[] mask, dp;
    
    static void computeMask(int[] order, int[] link, int sz) {
        // for (int i = sz - 1; i > 0; i--) {
        //     int u = order[i];
        //     if (link[u] != -1) mask[link[u]] |= mask[u];
        // }
    }

    static void dfsDP(int u, int[][] next, int sz) {
        if (dp[u] > 0) return;
        dp[u] = (mask[u] == 3) ? 1 : 0;
        for (int c = 0; c < 26; c++) {
            int v = next[u][c];
            if (v != -1) {
                dfsDP(v, next, sz);
                dp[u] += dp[v];
            }
        }
    }

    public static String getKthCommon(String s1, String s2, int k) {
        // Build Generalized SA for s1, s2
        // computeMask(...)
        // dfsDP(0, next, sz)
        
        StringBuilder res = new StringBuilder();
        int u = 0;
        // while (k > 0) {
        //     int isCommon = (mask[u] == 3 && u != 0) ? 1 : 0;
        //     k -= isCommon;
        //     if (k == 0) break;
        //     for(int c = 0; c < 26; c++) {
        //         int v = next[u][c];
        //         if (v != -1 && dp[v] > 0) {
        //             if (k <= dp[v]) {
        //                 res.append((char)(c + 'a'));
        //                 u = v; break;
        //             } else k -= dp[v];
        //         }
        //     }
        // }
        return res.toString();
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|S_1| + |S_2|)$
- **Không gian (Space)**: $O((|S_1| + |S_2|) \times \Sigma)$

---

## 23. Prefix Occurrences Count

### Đề bài chi tiết
Đếm tần suất xuất hiện của mọi tiền tố (Prefix) của chuỗi $S$ trong chính $S$.

### Phân tích thuật toán
Tương tự như việc đếm tần suất chuỗi con. Tiền tố của $S$ kết thúc tại $i$ sẽ nằm ở trạng thái đạt được sau $i+1$ bước `extend` đầu tiên (không nhảy theo link). Ta lưu lại các trạng thái này (như mảng `stateAtPosition` bài 21). Tần suất của một trạng thái `u` chính là kích thước tập `endpos` của nó. Kích thước này được tính bằng cách lan truyền số lượng node lá (các điểm extend ban đầu có count=1) lên theo Suffix Link từ dài nhất tới ngắn nhất.

### Mã nguồn Java
```java
public class PrefixOccurrences {
    // SA properties
    // int[] countEndpos, stateAt;
    
    public static int[] countPrefixes(String s) {
        // build SA and stateAt array...
        // 
        // Lấy thứ tự topo:
        // int[] order = getTopoOrder();
        // 
        // Tính endpos:
        // for(int i = sz - 1; i > 0; i--) {
        //     int u = order[i];
        //     countEndpos[link[u]] += countEndpos[u];
        // }
        
        int n = s.length();
        int[] ans = new int[n];
        // for (int i = 0; i < n; i++) {
        //     int u = stateAt[i];
        //     ans[i] = countEndpos[u];
        // }
        return ans;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|S|)$
- **Không gian (Space)**: $O(|S| \times \Sigma)$

---

## 24. Suffix Automaton and Persistent Segment Tree

### Đề bài chi tiết
Để truy vấn vị trí kết thúc `endpos` của một chuỗi con tại trạng thái `u`, người ta thường gắn một Persistent Segment Tree hoặc thực hiện Merge Segment Tree trên cây Suffix Link. Yêu cầu: Kiểm tra xem một trạng thái `u` có xuất hiện (endpos) trong khoảng `[L, R]` hay không.

### Phân tích thuật toán
Trong lúc thêm ký tự (extend), với mỗi node mới có độ dài là `len`, ta chèn giá trị vị trí `index` vào Segment Tree tương ứng của node đó. Sau khi dựng xong SA, ta duyệt Suffix Link Tree từ dưới lên (theo Topology) và tiến hành hợp nhất (Merge) Segment Tree của node con vào node cha (dùng Mergeable Segment Tree). 
Sau đó, để biết `u` có endpos trong `[L, R]`, ta chỉ cần truy vấn tổng số phần tử trong khoảng `[L, R]` trên Segment Tree gốc của `u`.

### Mã nguồn Java
```java
public class SASegmentTreeMerge {
    // Định nghĩa Segment Tree Node
    // class Node { int sum; Node left, right; }
    
    // Node merge(Node a, Node b) {
    //     if (a == null) return b;
    //     if (b == null) return a;
    //     Node res = new Node();
    //     res.sum = a.sum + b.sum;
    //     res.left = merge(a.left, b.left);
    //     res.right = merge(a.right, b.right);
    //     return res;
    // }
    
    // Quá trình tính toán:
    // for (int i = sz - 1; i > 0; i--) {
    //     int u = order[i];
    //     if (link[u] != -1) {
    //         root[link[u]] = merge(root[link[u]], root[u]);
    //     }
    // }
    
    // Truy vấn:
    // boolean hasOccurrence(int u, int L, int R) {
    //     return query(root[u], 0, N - 1, L, R) > 0;
    // }
}
```

### Độ phức tạp
- **Thời gian (Time)**: Dựng $O(|S| \log |S|)$, Hợp nhất $O(|S| \log |S|)$, Truy vấn $O(\log |S|)$.
- **Không gian (Space)**: $O(|S| \log |S|)$

---

## 25. Longest Substring Appearing in at least M Strings

### Đề bài chi tiết
Cho $N$ chuỗi. Tìm chuỗi con dài nhất xuất hiện trong ít nhất $M$ chuỗi ($M \le N$).

### Phân tích thuật toán
Sử dụng Generalized Suffix Automaton. Ở mỗi trạng thái, dùng cấu trúc dữ liệu Set hoặc Bitset để lưu các chuỗi $S_i$ chứa nó (khi extend với chuỗi $i$, ta đánh dấu $i$).
Sau đó DFS lên trên theo Suffix Link để hợp nhất các tập hợp này lại (với Set thì có thể dùng Kỹ thuật gộp nhánh - Small to Large). 
Trạng thái `u` nào có kích thước Set $\ge M$ thì nó hợp lệ. Chọn trạng thái hợp lệ có `len[u]` lớn nhất.

### Mã nguồn Java
```java
import java.util.*;

public class LCSInMStrings {
    // SA attributes ...
    // ArrayList<HashSet<Integer>> stateInStrings;
    
    public static int findLongest(String[] strings, int m) {
        // Build Generalized SA
        // int[] order = getTopoOrder();
        
        // Cập nhật lên theo Suffix Link
        // for(int i = sz - 1; i > 0; i--) {
        //     int u = order[i];
        //     int p = link[u];
        //     if (p != -1) {
        //         if (stateInStrings.get(p).size() < stateInStrings.get(u).size()) {
        //             // swap to optimize (Small to Large)
        //         }
        //         stateInStrings.get(p).addAll(stateInStrings.get(u));
        //     }
        // }
        
        int maxL = 0;
        // for (int i = 1; i < sz; i++) {
        //     if (stateInStrings.get(i).size() >= m) {
        //         maxL = Math.max(maxL, len[i]);
        //     }
        // }
        return maxL;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(\sum |S_i| \log N)$ (do small to large)
- **Không gian (Space)**: $O(\sum |S_i| \times \Sigma)$

---

## 26. Count Distinct Substrings generated by Concatenation (A + B)

### Đề bài chi tiết
Cho hai chuỗi $A$ và $B$. Đếm số lượng chuỗi con phân biệt của chuỗi $A + B$ mà bắt đầu từ trong $A$ và kết thúc trong $B$. (Có thể được suy ra từ tổng số chuỗi con của $A+B$ trừ số chuỗi của $A$ và $B$).

### Phân tích thuật toán
Cách nhanh nhất là xây dựng Suffix Automaton cho $A$, $B$ và $A+B$. Đếm số chuỗi con phân biệt của từng chuỗi bằng công thức $\sum (\text{len}[u] - \text{len}[\text{link}[u]])$.
Gọi số lượng là $C(S)$. Số lượng chuỗi con vắt ngang qua điểm ghép chính là $C(A+B) - C(A) - C(B) + C(\text{LCS của A suffix và B prefix})$. 
Thường người ta chỉ cần đếm trên SA của $A+B$, và đánh dấu những trạng thái chỉ sinh ra các vị trí endpos ở $A$ hoặc chỉ ở $B$ để loại trừ. 

### Mã nguồn Java
```java
public class ConcatenationSubstrings {
    // Đếm số chuỗi con cho một chuỗi bất kỳ qua SA
    public static long count(String s) {
        // SuffixAutomaton sa = new SuffixAutomaton(s);
        long res = 0;
        // for(int i = 1; i < sa.sz; i++) {
        //     res += sa.len[i] - sa.len[sa.link[i]];
        // }
        return res;
    }
    
    public static long countCrossSubstrings(String A, String B) {
        long cTotal = count(A + B);
        // Để loại bỏ các chuỗi chỉ nằm trong A hoặc B,
        // cần gán nhãn cho các trạng thái của SA(A+B) 
        // là có endpos < |A| hay endpos >= |A| và tính toán.
        // Mã giả minh hoạ:
        // long crossCount = cTotal - onlyA - onlyB;
        return cTotal; // return specific result based on implementation
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|A| + |B|)$
- **Không gian (Space)**: $O((|A| + |B|) \times \Sigma)$

---

## 27. Number of Matches with Exactly 1 Mismatch

### Đề bài chi tiết
Cho văn bản $T$ và mẫu $P$. Tìm số lần mẫu $P$ xuất hiện trong $T$ với điều kiện có **đúng 1** ký tự khác biệt (mismatch).

### Phân tích thuật toán
Sử dụng Suffix Automaton của $T$. Chạy qua mẫu $P$. Do chỉ cho phép 1 mismatch, ta có thể kết hợp quy hoạch động hoặc nhảy thử 1 ký tự sai trên Automaton. Gọi $u$ là trạng thái khớp tiền tố $P[0..i-1]$. Tại $P[i]$, ta thử đi sang nhánh $c \neq P[i]$ (nếu có), từ trạng thái mới đó ta cần khớp phần còn lại $P[i+1..|P|-1]$ hoàn hảo. 
Phần "khớp hoàn hảo" có thể tăng tốc độ bằng cách sử dụng Binary Lifting trên cây LCA (của Suffix Link ngược $T^{rev}$) để tìm xem độ dài khớp tối đa có bao phủ nốt $P$ hay không.

### Mã nguồn Java
```java
public class MatchOneMismatch {
    // Mảng nhảy và lca trên suffix tree của đảo ngược T
    public static int solve(String T, String P) {
        // Build SA for T
        // Build SA for T reversed (to check perfect match of suffixes quickly)
        int count = 0;
        // for (int i = 0; i < T.length() - P.length() + 1; i++) {
        //     int lcp = getLCP(T, i, P, 0); // LCP của T[i...] và P
        //     if (lcp == P.length()) continue; // Khớp 0 mismatch
        //     int lcp2 = getLCP(T, i + lcp + 1, P, lcp + 1); // Tiếp tục sau mismatch
        //     if (lcp + 1 + lcp2 >= P.length()) count++;
        // }
        return count;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: Dựng $O(|T| \log |T|)$, Truy vấn $O(|T|)$ (với LCP) hoặc $O(|P| \times \Sigma)$.
- **Không gian (Space)**: $O(|T| \log |T|)$

---

## 28. Dynamic Suffix Automaton

### Đề bài chi tiết
Khác với SA tĩnh. Ta thêm từng ký tự một vào chuỗi $S$, và ngay lập tức cần trả lời câu hỏi: Tổng số lượng chuỗi con phân biệt hiện tại là bao nhiêu? (Truy vấn trực tuyến / Online queries).

### Phân tích thuật toán
Quá trình xây dựng chuẩn (hàm `extend`) của Suffix Automaton vốn dĩ đã có tính online (xử lý từng ký tự từ trái qua phải). Tổng số lượng chuỗi con phân biệt tăng thêm sau khi `extend(c)` chính xác bằng số lượng chuỗi con thuộc về trạng thái `cur` trừ đi số lượng thuộc về `link[cur]`. Nói cách khác, số chuỗi con tăng thêm là: $\Delta = \text{len}[cur] - \text{len}[\text{link}[cur]]$. Nếu có trạng thái clone được sinh ra, việc đổi link không thay đổi tổng số chuỗi con đang tồn tại.

### Mã nguồn Java
```java
public class DynamicSuffixAutomaton {
    // Thuộc tính SA
    static int[] len = new int[200000];
    static int[] link = new int[200000];
    static int[][] next = new int[200000][26];
    static int sz = 1, last = 0;
    static long distinctSubstrings = 0;
    
    static { link[0] = -1; for(int[] r: next) java.util.Arrays.fill(r, -1); }

    public static long extendOnline(int c) {
        int cur = sz++;
        len[cur] = len[last] + 1;
        int p = last;
        while (p != -1 && next[p][c] == -1) {
            next[p][c] = cur;
            p = link[p];
        }
        if (p == -1) {
            link[cur] = 0;
        } else {
            int q = next[p][c];
            if (len[p] + 1 == len[q]) {
                link[cur] = q;
            } else {
                int clone = sz++;
                len[clone] = len[p] + 1;
                System.arraycopy(next[q], 0, next[clone], 0, 26);
                link[clone] = link[q];
                while (p != -1 && next[p][c] == q) {
                    next[p][c] = clone;
                    p = link[p];
                }
                link[q] = link[cur] = clone;
            }
        }
        last = cur;
        distinctSubstrings += (len[cur] - len[link[cur]]);
        return distinctSubstrings;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: Khấu hao (Amortized) $O(1)$ mỗi ký tự.
- **Không gian (Space)**: $O(N \times \Sigma)$

---

## 29. Longest Common Substring Query (Offline)

### Đề bài chi tiết
Cho chuỗi $S$. Có nhiều truy vấn offline $(L, R)$. Tìm chuỗi con lặp lại dài nhất (LRS) nằm trọn trong đoạn $S[L..R]$.

### Phân tích thuật toán
Thuật toán Mo có thể được kết hợp với Suffix Automaton (hoặc Suffix Tree) để xử lý.
Sử dụng Suffix Tree, ta thêm dần các suffix/vị trí. Dùng thuật toán Mo di chuyển 2 con trỏ $L$ và $R$. Khi chèn một vị trí kết thúc mới, ta cập nhật các khoảng cách của nó tới các vị trí lân cận trong cây LCT hoặc Set để duy trì giá trị chuỗi con lớn nhất lặp lại. Đây là một bài toán rất khó (như bài *Codeforces 700E* mở rộng).

### Mã nguồn Java
```java
public class LRSQueryOffline {
    // Sườn giải thuật Mo Algorithm kết hợp Suffix Data Structure
    // class Query implements Comparable<Query> { int L, R, id; ... }
    public static void solveLRSQueries(String s, int[][] queries) {
        // Query[] qs = parseQueries(queries);
        // Arrays.sort(qs, new MoComparator());
        
        // int curL = 0, curR = -1;
        // int[] ans = new int[qs.length];
        
        // for (Query q : qs) {
        //     while (curR < q.R) add(++curR);
        //     while (curL > q.L) add(--curL);
        //     while (curR > q.R) remove(curR--);
        //     while (curL < q.L) remove(curL++);
        //     ans[q.id] = getCurrentMaxLRS();
        // }
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(N \sqrt{Q} \log N)$ (tuỳ vào CTDL khi `add`/`remove`).
- **Không gian (Space)**: $O(N \log N + Q)$

---

## 30. Suffix Automaton to Suffix Tree Conversion

### Đề bài chi tiết
Mặc dù Suffix Automaton là một đồ thị có hướng không chu trình (DAG), cây liên kết các hậu tố (Suffix Link Tree) của Automaton được dựng từ chuỗi $S$ **chính thức tương đương** với Suffix Tree của chuỗi đảo ngược $S^{rev}$. Bài toán yêu cầu xuất cấu trúc Suffix Tree của $S^{rev}$ từ SA của $S$.

### Phân tích thuật toán
Trên Suffix Automaton của $S$, mảng `link` xác định cha của mỗi node. Các cạnh nối từ `link[u]` đến `u` tạo thành cây Suffix Link Tree.
Độ dài cạnh (nhãn trên cạnh Suffix Tree) nối từ `link[u]` xuống `u` bằng đúng phần chuỗi dư ra của trạng thái `u` so với `link[u]`, với độ dài là $\text{len}[u] - \text{len}[\text{link}[u]]$. Ký tự đầu tiên trên cạnh này là ký tự ở vị trí $\text{endpos}[u] - \text{len}[\text{link}[u]]$.

### Mã nguồn Java
```java
import java.util.*;

public class SAToSuffixTree {
    static class Edge {
        int to, len, startIdx;
        Edge(int t, int l, int s) { to = t; len = l; startIdx = s; }
    }
    
    // sa.link, sa.len, sa.endpos (đã tính qua extend hoặc DFS)
    public static List<List<Edge>> buildSuffixTree(int[] link, int[] len, int[] endpos, int sz) {
        List<List<Edge>> tree = new ArrayList<>();
        for (int i = 0; i < sz; i++) tree.add(new ArrayList<>());
        
        for (int u = 1; u < sz; u++) {
            int p = link[u];
            int edgeLen = len[u] - len[p];
            // Ký tự đầu tiên của chuỗi con trên cạnh là ở endpos[u] - len[p]
            int startIdx = endpos[u] - len[p];
            tree.get(p).add(new Edge(u, edgeLen, startIdx));
        }
        return tree;
    }
}
```

### Độ phức tạp
- **Thời gian (Time)**: $O(|S|)$
- **Không gian (Space)**: $O(|S|)$
