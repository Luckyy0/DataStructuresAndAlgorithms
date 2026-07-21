---
title: Rabin-Karp Algorithm Practice Problems
description: 30 coding problems to master Rabin-Karp Algorithm and Rolling Hash
language: vi
---

# 30 Bài tập Rabin-Karp Algorithm

## 1. Repeated DNA Sequences
**Đề bài chi tiết:** Cho chuỗi DNA chỉ chứa 'A', 'C', 'G', 'T'. Tìm tất cả các chuỗi con độ dài 10 xuất hiện nhiều hơn một lần.
**Phân tích thuật toán:** Sử dụng Rolling Hash với base $B=4$. Do $B$ nhỏ và độ dài cố định $L=10$, mã băm sẽ không bị tràn int (tối đa $4^{10}$), nên có thể lưu trực tiếp vào HashSet.
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public List<String> findRepeatedDnaSequences(String s) {
        if (s.length() < 10) return new ArrayList<>();
        Set<Integer> seen = new HashSet<>();
        Set<String> result = new HashSet<>();
        int[] map = new int[128];
        map['A'] = 0; map['C'] = 1; map['G'] = 2; map['T'] = 3;
        
        int hash = 0;
        int L = 10;
        int mask = (1 << (2 * L)) - 1; 
        
        for (int i = 0; i < s.length(); i++) {
            hash = (hash << 2) | map[s.charAt(i)];
            hash &= mask;
            if (i >= L - 1) {
                if (!seen.add(hash)) {
                    result.add(s.substring(i - L + 1, i + 1));
                }
            }
        }
        return new ArrayList<>(result);
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N)$ thời gian, $\mathcal{O}(N)$ không gian.

## 2. Find All Occurrences of Pattern (Rabin-Karp)
**Đề bài chi tiết:** Cho chuỗi `text` và `pattern`. Tìm tất cả các chỉ số bắt đầu của `pattern` trong `text`.
**Phân tích thuật toán:** Cài đặt thuật toán Rabin-Karp chuẩn để trả về danh sách các chỉ số.
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public List<Integer> search(String pat, String txt) {
        List<Integer> res = new ArrayList<>();
        int q = 1000000007;
        int d = 256;
        int m = pat.length();
        int n = txt.length();
        if (m > n || m == 0) return res;

        long p = 0, t = 0, h = 1;
        for (int i = 0; i < m - 1; i++) h = (h * d) % q;

        for (int i = 0; i < m; i++) {
            p = (d * p + pat.charAt(i)) % q;
            t = (d * t + txt.charAt(i)) % q;
        }

        for (int i = 0; i <= n - m; i++) {
            if (p == t) {
                boolean match = true;
                for (int j = 0; j < m; j++) {
                    if (txt.charAt(i + j) != pat.charAt(j)) {
                        match = false; break;
                    }
                }
                if (match) res.add(i);
            }
            if (i < n - m) {
                t = (d * (t - txt.charAt(i) * h) + txt.charAt(i + m)) % q;
                if (t < 0) t = (t + q);
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N + M)$ thời gian, $\mathcal{O}(1)$ không gian (không kể output).

## 3. Longest Duplicate Substring
**Đề bài chi tiết:** Tìm chuỗi con lặp lại dài nhất trong chuỗi $S$. Nếu có nhiều kết quả, trả về bất kỳ.
**Phân tích thuật toán:** Dùng Binary Search để tìm độ dài chuỗi lặp lại lớn nhất kết hợp với Rabin-Karp (Rolling Hash) để kiểm tra xem có chuỗi con nào cùng độ dài xuất hiện $\ge 2$ lần hay không.
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    long q = (1L << 31) - 1;
    long R = 256;
    
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
        long h = 0, RM = 1;
        for (int i = 1; i <= len - 1; i++) RM = (R * RM) % q;
        for (int i = 0; i < len; i++) h = (h * R + s.charAt(i)) % q;
        
        Set<Long> seen = new HashSet<>();
        seen.add(h);
        
        for (int i = len; i < s.length(); i++) {
            h = (h + q - RM * s.charAt(i - len) % q) % q;
            h = (h * R + s.charAt(i)) % q;
            if (!seen.add(h)) {
                return s.substring(i - len + 1, i + 1);
            }
        }
        return null;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N \log N)$ thời gian, $\mathcal{O}(N)$ không gian.

## 4. Longest Common Subpath
**Đề bài chi tiết:** Có $n$ paths, mỗi path là mảng các số. Tìm path chung dài nhất xuất hiện trong tất cả các paths.
**Phân tích thuật toán:** Tương tự Binary Search độ dài kết hợp Rolling Hash (Rabin-Karp) với tập dữ liệu dạng int array thay vì char.
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    long mod = 1000000000039L;
    long base = 100003;

    public int longestCommonSubpath(int n, int[][] paths) {
        int minLen = Integer.MAX_VALUE;
        for (int[] p : paths) minLen = Math.min(minLen, p.length);
        
        int l = 1, r = minLen, ans = 0;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (check(paths, mid)) {
                ans = mid;
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        return ans;
    }

    private boolean check(int[][] paths, int len) {
        long RM = 1;
        for (int i = 0; i < len; i++) RM = (RM * base) % mod;
        
        Set<Long> common = null;
        for (int[] p : paths) {
            Set<Long> current = new HashSet<>();
            long hash = 0;
            for (int i = 0; i < p.length; i++) {
                hash = (hash * base + p[i]) % mod;
                if (i >= len) {
                    hash = (hash - p[i - len] * RM % mod + mod) % mod;
                }
                if (i >= len - 1) current.add(hash);
            }
            if (common == null) {
                common = current;
            } else {
                common.retainAll(current);
            }
            if (common.isEmpty()) return false;
        }
        return true;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N \log L)$ thời gian với $N$ là tổng chiều dài paths, $\mathcal{O}(N)$ không gian.

## 5. String Matching in an Array
**Đề bài chi tiết:** Cho mảng `words`. Trả về danh sách các từ là chuỗi con của ít nhất một từ khác trong mảng.
**Phân tích thuật toán:** So sánh từng cặp từ, nếu `words[i]` nhỏ hơn và là chuỗi con của `words[j]`, lưu lại `words[i]`. Có thể dùng Rabin-Karp hoặc indexOf().
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public List<String> stringMatching(String[] words) {
        Set<String> res = new HashSet<>();
        for (int i = 0; i < words.length; i++) {
            for (int j = 0; j < words.length; j++) {
                if (i != j && words[j].contains(words[i])) {
                    res.add(words[i]);
                }
            }
        }
        return new ArrayList<>(res);
    }
}
```
**Độ phức tạp:** $\mathcal{O}(K^2 \cdot M)$ với $K$ là số từ, $M$ là chiều dài trung bình.

## 6. Distinct Echo Substrings
**Đề bài chi tiết:** Tìm số lượng chuỗi con có dạng `A + A` (A lặp 2 lần liên tiếp). Mỗi chuỗi con `A+A` chỉ tính 1 lần.
**Phân tích thuật toán:** Tính Hash trước (Prefix Hash) để trích xuất hash của bất kỳ chuỗi con $\mathcal{O}(1)$. Thử mọi độ dài $L$, so sánh hash phần bên trái và bên phải.
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public int distinctEchoSubstrings(String text) {
        int n = text.length();
        long[] hash = new long[n + 1];
        long[] pow = new long[n + 1];
        long base = 256;
        long mod = 1000000007;

        pow[0] = 1;
        for (int i = 1; i <= n; i++) {
            hash[i] = (hash[i - 1] * base + text.charAt(i - 1)) % mod;
            pow[i] = (pow[i - 1] * base) % mod;
        }

        Set<Long> set = new HashSet<>();
        for (int len = 1; len <= n / 2; len++) {
            for (int i = 0; i <= n - 2 * len; i++) {
                long h1 = getHash(hash, pow, i, i + len, mod);
                long h2 = getHash(hash, pow, i + len, i + 2 * len, mod);
                if (h1 == h2) set.add(h1);
            }
        }
        return set.size();
    }

    private long getHash(long[] hash, long[] pow, int l, int r, long mod) {
        long res = (hash[r] - hash[l] * pow[r - l] % mod + mod) % mod;
        return res;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N^2)$ thời gian, $\mathcal{O}(N)$ không gian.

## 7. Shortest Palindrome (Rolling Hash)
**Đề bài chi tiết:** Cho chuỗi $s$, thêm tiền tố ngắn nhất để tạo thành chuỗi Palindrome.
**Phân tích thuật toán:** Tìm độ dài tiền tố Palindrome dài nhất trong chuỗi $s$. Có thể tính Hash từ trái sang phải và Hash từ phải sang trái. Nếu 2 hash bằng nhau, đoạn từ $0 \dots i$ là Palindrome.
**Mã nguồn Java:**
```java
public class Solution {
    public String shortestPalindrome(String s) {
        long hash1 = 0, hash2 = 0, base = 29, mod = 1000000007;
        long pow = 1;
        int bestLen = 0;
        
        for (int i = 0; i < s.length(); i++) {
            int val = s.charAt(i) - 'a' + 1;
            hash1 = (hash1 * base + val) % mod;
            hash2 = (hash2 + val * pow) % mod;
            if (hash1 == hash2) bestLen = i + 1;
            pow = (pow * base) % mod;
        }
        
        String suffix = s.substring(bestLen);
        return new StringBuilder(suffix).reverse().toString() + s;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N)$ thời gian, $\mathcal{O}(N)$ không gian.

## 8. Longest Happy Prefix (Rolling Hash)
**Đề bài chi tiết:** Tìm chuỗi vừa là tiền tố, vừa là hậu tố dài nhất của chuỗi $s$ (không tính chính chuỗi đó).
**Phân tích thuật toán:** Dùng Rolling Hash, vừa xây dựng prefix hash từ đầu, vừa xây dựng suffix hash từ cuối. Vị trí nào chúng bằng nhau sẽ cập nhật kết quả dài nhất.
**Mã nguồn Java:**
```java
public class Solution {
    public String longestPrefix(String s) {
        long prefixHash = 0, suffixHash = 0, pow = 1, mod = 1000000007, base = 31;
        int bestLen = 0;
        
        for (int i = 0; i < s.length() - 1; i++) {
            int leftVal = s.charAt(i) - 'a';
            int rightVal = s.charAt(s.length() - 1 - i) - 'a';
            
            prefixHash = (prefixHash * base + leftVal) % mod;
            suffixHash = (suffixHash + rightVal * pow) % mod;
            pow = (pow * base) % mod;
            
            if (prefixHash == suffixHash) {
                bestLen = i + 1;
            }
        }
        return s.substring(0, bestLen);
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N)$ thời gian, $\mathcal{O}(1)$ không gian (bỏ qua string creation).

## 9. Find the Index of the First Occurrence in a String (Rabin-Karp)
**Đề bài chi tiết:** Cài đặt hàm indexOf(). Tìm vị trí đầu tiên của `needle` trong `haystack`.
**Phân tích thuật toán:** Sử dụng Rabin-Karp với single pattern matching thay vì KMP.
**Mã nguồn Java:**
```java
public class Solution {
    public int strStr(String haystack, String needle) {
        if (needle.length() == 0) return 0;
        if (haystack.length() < needle.length()) return -1;
        
        long h = 1, pHash = 0, tHash = 0;
        long B = 256, Q = 1000000007;
        int m = needle.length(), n = haystack.length();
        
        for (int i = 0; i < m - 1; i++) h = (h * B) % Q;
        for (int i = 0; i < m; i++) {
            pHash = (B * pHash + needle.charAt(i)) % Q;
            tHash = (B * tHash + haystack.charAt(i)) % Q;
        }
        
        for (int i = 0; i <= n - m; i++) {
            if (pHash == tHash) {
                if (haystack.substring(i, i + m).equals(needle)) return i;
            }
            if (i < n - m) {
                tHash = (B * (tHash - haystack.charAt(i) * h) + haystack.charAt(i + m)) % Q;
                if (tHash < 0) tHash += Q;
            }
        }
        return -1;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N + M)$ thời gian, $\mathcal{O}(1)$ không gian.

## 10. Check if a String Contains All Length-K Binary Codes
**Đề bài chi tiết:** Cho chuỗi $s$ gồm '0' và '1', số $k$. Kiểm tra xem mọi mã nhị phân độ dài $k$ có xuất hiện dưới dạng chuỗi con không.
**Phân tích thuật toán:** Vì bảng chữ cái chỉ là 2 (0, 1), ta coi $s$ như số hệ cơ số 2. Dùng bitwise shift để tính Rolling Hash, đẩy vào HashSet. Cuối cùng HashSet cần đủ $2^k$ phần tử.
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public boolean hasAllCodes(String s, int k) {
        if (s.length() < (1 << k) + k - 1) return false;
        
        boolean[] seen = new boolean[1 << k];
        int count = 1 << k;
        int hash = 0;
        int mask = (1 << k) - 1;
        
        for (int i = 0; i < s.length(); i++) {
            hash = ((hash << 1) & mask) | (s.charAt(i) - '0');
            if (i >= k - 1 && !seen[hash]) {
                seen[hash] = true;
                count--;
                if (count == 0) return true;
            }
        }
        return count == 0;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N)$ thời gian, $\mathcal{O}(2^k)$ không gian.

## 11. Substring with Concatenation of All Words
**Đề bài chi tiết:** Cho chuỗi `s` và mảng các từ `words` có cùng độ dài. Tìm tất cả các chỉ số bắt đầu của các chuỗi con trong `s` là sự kết nối của tất cả các từ trong `words` theo một thứ tự bất kỳ, mỗi từ xuất hiện đúng một lần và không có bất kỳ ký tự nào xen giữa.
**Phân tích thuật toán:** Mặc dù bài toán thường được giải bằng Sliding Window và HashMap, ta có thể sử dụng Rolling Hash để tính giá trị băm cho từng từ trong `words` và từng đoạn con trong `s`. Với các từ có cùng độ dài, ta coi mỗi từ là một "ký tự" lớn và tính tổng hoặc giá trị băm kết hợp để kiểm tra sự khớp nối của cả khối các từ trong $\mathcal{O}(1)$ sau khi tiền xử lý.
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public List<Integer> findSubstring(String s, String[] words) {
        List<Integer> res = new ArrayList<>();
        if (s == null || s.length() == 0 || words == null || words.length == 0) return res;
        
        int wordLen = words[0].length();
        int numWords = words.length;
        int totalLen = wordLen * numWords;
        
        if (s.length() < totalLen) return res;
        
        Map<Long, Integer> wordHashCount = new HashMap<>();
        long base = 256;
        long mod = 1000000007;
        
        for (String word : words) {
            long h = getHash(word, base, mod);
            wordHashCount.put(h, wordHashCount.getOrDefault(h, 0) + 1);
        }
        
        long[] hash = new long[s.length() + 1];
        long[] pow = new long[s.length() + 1];
        pow[0] = 1;
        for (int i = 1; i <= s.length(); i++) {
            hash[i] = (hash[i - 1] * base + s.charAt(i - 1)) % mod;
            pow[i] = (pow[i - 1] * base) % mod;
        }
        
        for (int i = 0; i < wordLen; i++) {
            int left = i;
            int right = i;
            Map<Long, Integer> currentCount = new HashMap<>();
            int wordsMatched = 0;
            
            while (right + wordLen <= s.length()) {
                long h = getSubstringHash(hash, pow, right, right + wordLen, mod);
                right += wordLen;
                
                if (wordHashCount.containsKey(h)) {
                    currentCount.put(h, currentCount.getOrDefault(h, 0) + 1);
                    wordsMatched++;
                    
                    while (currentCount.get(h) > wordHashCount.get(h)) {
                        long leftHash = getSubstringHash(hash, pow, left, left + wordLen, mod);
                        currentCount.put(leftHash, currentCount.get(leftHash) - 1);
                        wordsMatched--;
                        left += wordLen;
                    }
                    
                    if (wordsMatched == numWords) {
                        res.add(left);
                    }
                } else {
                    currentCount.clear();
                    wordsMatched = 0;
                    left = right;
                }
            }
        }
        return res;
    }
    
    private long getHash(String s, long base, long mod) {
        long h = 0;
        for (char c : s.toCharArray()) {
            h = (h * base + c) % mod;
        }
        return h;
    }
    
    private long getSubstringHash(long[] hash, long[] pow, int l, int r, long mod) {
        return (hash[r] - hash[l] * pow[r - l] % mod + mod) % mod;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N)$ thời gian, $\mathcal{O}(N)$ không gian với $N$ là chiều dài chuỗi $s$.

## 12. Implement KMP Algorithm vs Rabin-Karp
**Đề bài chi tiết:** Cài đặt thuật toán Rabin-Karp để tìm tất cả các vị trí xuất hiện của mẫu `pattern` trong văn bản `text`. Đây là một bài thực hành tiêu chuẩn nhằm nắm vững cách tính toán Rolling Hash trên các luồng văn bản (streams).
**Phân tích thuật toán:** Thuật toán tính mã băm ban đầu cho `pattern` và cửa sổ độ dài $M$ đầu tiên của `text`. Khi cửa sổ trượt đi một ký tự, ta trừ giá trị của ký tự bị loại bỏ và cộng thêm ký tự mới vào mã băm. Nếu hai mã băm khớp nhau, ta so sánh chuỗi thực sự để tránh va chạm băm (hash collision).
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public List<Integer> searchRabinKarp(String text, String pattern) {
        List<Integer> result = new ArrayList<>();
        if (pattern.length() == 0 || text.length() < pattern.length()) return result;
        
        long pHash = 0, tHash = 0;
        long base = 256, mod = 1000000007;
        long h = 1;
        int m = pattern.length(), n = text.length();
        
        for (int i = 0; i < m - 1; i++) {
            h = (h * base) % mod;
        }
        
        for (int i = 0; i < m; i++) {
            pHash = (base * pHash + pattern.charAt(i)) % mod;
            tHash = (base * tHash + text.charAt(i)) % mod;
        }
        
        for (int i = 0; i <= n - m; i++) {
            if (pHash == tHash) {
                if (text.substring(i, i + m).equals(pattern)) {
                    result.add(i);
                }
            }
            if (i < n - m) {
                tHash = (base * (tHash - text.charAt(i) * h) + text.charAt(i + m)) % mod;
                if (tHash < 0) tHash += mod;
            }
        }
        
        return result;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N + M)$ thời gian trung bình, $\mathcal{O}(N \times M)$ trong trường hợp xấu nhất nếu có nhiều va chạm, $\mathcal{O}(1)$ không gian lưu trữ thêm.

## 13. Maximum Length of Repeated Subarray
**Đề bài chi tiết:** Cho hai mảng số nguyên `nums1` và `nums2`. Trả về độ dài lớn nhất của mảng con xuất hiện trong cả hai mảng.
**Phân tích thuật toán:** Sử dụng Binary Search trên độ dài mảng con (từ $0$ đến $\min(|nums1|, |nums2|)$). Để kiểm tra xem có mảng con chung độ dài $L$ hay không, tính tất cả các giá trị băm của các mảng con độ dài $L$ trong `nums1` và lưu vào HashSet, sau đó kiểm tra xem có mảng con độ dài $L$ trong `nums2` có giá trị băm nằm trong HashSet hay không.
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    long base = 101;
    long mod = (long) 1e9 + 9;
    
    public int findLength(int[] nums1, int[] nums2) {
        int left = 1, right = Math.min(nums1.length, nums2.length);
        int ans = 0;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (check(nums1, nums2, mid)) {
                ans = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return ans;
    }
    
    private boolean check(int[] nums1, int[] nums2, int len) {
        Set<Long> hashes = new HashSet<>();
        long h = 0, pow = 1;
        
        for (int i = 1; i <= len - 1; i++) pow = (pow * base) % mod;
        
        for (int i = 0; i < nums1.length; i++) {
            h = (h * base + nums1[i]) % mod;
            if (i >= len) {
                h = (h - nums1[i - len] * pow % mod + mod) % mod;
            }
            if (i >= len - 1) hashes.add(h);
        }
        
        h = 0;
        for (int i = 0; i < nums2.length; i++) {
            h = (h * base + nums2[i]) % mod;
            if (i >= len) {
                h = (h - nums2[i - len] * pow % mod + mod) % mod;
            }
            if (i >= len - 1 && hashes.contains(h)) {
                return true;
            }
        }
        
        return false;
    }
}
```
**Độ phức tạp:** $\mathcal{O}((N + M) \log(\min(N, M)))$ thời gian, $\mathcal{O}(N)$ không gian.

## 14. Longest Chunked Palindrome Decomposition
**Đề bài chi tiết:** Cho một chuỗi `text`. Bạn cần chia nó thành $k$ phần $subtext_1, subtext_2, \dots, subtext_k$ sao cho $subtext_i = subtext_{k - i + 1}$ với mọi $1 \le i \le k$ và $k$ là lớn nhất.
**Phân tích thuật toán:** Sử dụng thuật toán tham lam (Greedy) kết hợp với Rolling Hash hai đầu. So sánh giá trị băm từ đầu chuỗi (trái sang phải) và từ cuối chuỗi (phải sang trái). Khi hai giá trị băm bằng nhau (và chuỗi thực sự bằng nhau), ta cắt chúng thành hai phần của palindrome và tiếp tục với phần còn lại ở giữa.
**Mã nguồn Java:**
```java
public class Solution {
    public int longestDecomposition(String text) {
        int n = text.length();
        int left = 0, right = n - 1;
        long leftHash = 0, rightHash = 0;
        long base = 256, mod = 1000000007, pow = 1;
        int count = 0;
        int len = 0;
        
        while (left < right) {
            leftHash = (leftHash * base + text.charAt(left)) % mod;
            rightHash = (rightHash + text.charAt(right) * pow) % mod;
            pow = (pow * base) % mod;
            len++;
            
            if (leftHash == rightHash) {
                if (text.substring(left - len + 1, left + 1).equals(text.substring(right, right + len))) {
                    count += 2;
                    leftHash = 0; rightHash = 0; pow = 1; len = 0;
                }
            }
            left++;
            right--;
        }
        
        if (len > 0 || left == right) {
            count++;
        }
        return count;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N)$ thời gian, $\mathcal{O}(1)$ không gian (bỏ qua không gian để tạo chuỗi con so sánh).

## 15. Camelcase Matching
**Đề bài chi tiết:** Cho một mảng các chuỗi `queries` và một mẫu `pattern`. Một chuỗi truy vấn khớp với mẫu nếu ta có thể chèn các chữ cái thường vào mẫu để tạo thành truy vấn. Trả về mảng boolean tương ứng.
**Phân tích thuật toán:** Mặc dù bài toán này có thể dùng Two Pointers, ta có thể áp dụng tư tưởng Hashing để lọc nhanh các chuỗi không khớp bằng cách so sánh số lượng hoặc mã băm của các chữ cái viết hoa trong truy vấn và mẫu. Nếu các chữ cái viết hoa không hoàn toàn khớp nhau, có thể bỏ qua ngay. (Dưới đây trình bày cách duyệt mảng chuẩn mực nhưng tối ưu để xác định chuỗi con).
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public List<Boolean> camelMatch(String[] queries, String pattern) {
        List<Boolean> result = new ArrayList<>();
        for (String query : queries) {
            result.add(isMatch(query, pattern));
        }
        return result;
    }
    
    private boolean isMatch(String query, String pattern) {
        int i = 0, j = 0;
        while (i < query.length()) {
            if (j < pattern.length() && query.charAt(i) == pattern.charAt(j)) {
                i++;
                j++;
            } else if (Character.isUpperCase(query.charAt(i))) {
                return false;
            } else {
                i++;
            }
        }
        return j == pattern.length();
    }
}
```
**Độ phức tạp:** $\mathcal{O}(K \cdot N)$ thời gian với $K$ là số truy vấn, $N$ là chiều dài lớn nhất của truy vấn, $\mathcal{O}(1)$ không gian bổ sung.

## 16. Maximum Number of Occurrences of a Substring
**Đề bài chi tiết:** Cho chuỗi $s$, trả về số lần xuất hiện nhiều nhất của một chuỗi con thỏa mãn điều kiện: số ký tự phân biệt $\le$ `maxLetters`, độ dài nằm trong đoạn `[minSize, maxSize]`.
**Phân tích thuật toán:** Bất kỳ chuỗi con nào có độ dài lớn hơn `minSize` mà xuất hiện nhiều lần thì tiền tố độ dài `minSize` của nó cũng xuất hiện ít nhất ngần ấy lần. Vậy chỉ cần kiểm tra các chuỗi con độ dài đúng bằng `minSize`. Dùng Rolling Hash để đếm số lần xuất hiện và mảng tần số để kiểm tra số lượng ký tự phân biệt.
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public int maxFreq(String s, int maxLetters, int minSize, int maxSize) {
        Map<Long, Integer> hashCount = new HashMap<>();
        int[] charCount = new int[26];
        int unique = 0;
        long base = 31, mod = (long) 1e9 + 7, pow = 1;
        long hash = 0;
        int maxOccurrences = 0;
        
        for (int i = 0; i < minSize - 1; i++) {
            pow = (pow * base) % mod;
        }
        
        for (int i = 0; i < s.length(); i++) {
            int c = s.charAt(i) - 'a';
            if (charCount[c]++ == 0) unique++;
            hash = (hash * base + c) % mod;
            
            if (i >= minSize) {
                int leftChar = s.charAt(i - minSize) - 'a';
                if (--charCount[leftChar] == 0) unique--;
                hash = (hash - leftChar * pow % mod + mod) % mod;
            }
            
            if (i >= minSize - 1) {
                if (unique <= maxLetters) {
                    hashCount.put(hash, hashCount.getOrDefault(hash, 0) + 1);
                    maxOccurrences = Math.max(maxOccurrences, hashCount.get(hash));
                }
            }
        }
        return maxOccurrences;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N)$ thời gian, $\mathcal{O}(N)$ không gian.

## 17. K-th Smallest Substring in Lexicographical Order
**Đề bài chi tiết:** Cho chuỗi $s$ và số nguyên $K$. Tìm chuỗi con nhỏ thứ $K$ theo thứ tự từ điển của chuỗi $s$.
**Phân tích thuật toán:** Bằng cách kết hợp mảng Suffix Array cơ bản (sắp xếp các hậu tố bằng Binary Search + LCP qua Hashing), ta có thể đếm số lượng chuỗi con phân biệt và xác định chuỗi nhỏ thứ $K$. Dùng Rolling Hash để tìm LCP (Longest Common Prefix) giữa hai hậu tố trong $\mathcal{O}(\log N)$.
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    long[] hash, pow;
    long base = 256, mod = 1000000007;

    public String kthSmallestSubstring(String s, int k) {
        int n = s.length();
        hash = new long[n + 1];
        pow = new long[n + 1];
        pow[0] = 1;
        Integer[] suffixes = new Integer[n];
        
        for (int i = 0; i < n; i++) {
            suffixes[i] = i;
            hash[i + 1] = (hash[i] * base + s.charAt(i)) % mod;
            pow[i + 1] = (pow[i] * base) % mod;
        }
        
        Arrays.sort(suffixes, (a, b) -> {
            int lcp = getLCP(s, a, b);
            if (a + lcp == n) return -1;
            if (b + lcp == n) return 1;
            return s.charAt(a + lcp) - s.charAt(b + lcp);
        });
        
        int count = 0;
        int lcpPrev = 0;
        for (int i = 0; i < n; i++) {
            int idx = suffixes[i];
            if (i > 0) lcpPrev = getLCP(s, suffixes[i - 1], idx);
            else lcpPrev = 0;
            
            int len = n - idx;
            if (count + (len - lcpPrev) >= k) {
                return s.substring(idx, idx + lcpPrev + k - count);
            }
            count += (len - lcpPrev);
        }
        return "";
    }
    
    private int getLCP(String s, int a, int b) {
        int left = 1, right = Math.min(s.length() - a, s.length() - b), lcp = 0;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (getHash(a, a + mid) == getHash(b, b + mid)) {
                lcp = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return lcp;
    }
    
    private long getHash(int l, int r) {
        return (hash[r] - hash[l] * pow[r - l] % mod + mod) % mod;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N \log^2 N)$ thời gian cho việc sắp xếp bằng Binary Search LCP, $\mathcal{O}(N)$ không gian.

## 18. Count Unique Substrings
**Đề bài chi tiết:** Đếm số lượng chuỗi con phân biệt của một chuỗi $s$ cho trước.
**Phân tích thuật toán:** Xây dựng ma trận băm hoặc mảng băm tiền tố để lấy mã băm của bất kỳ chuỗi con nào trong $\mathcal{O}(1)$. Lưu mọi mã băm vào HashSet và trả về kích thước tập hợp. Có thể sinh mã băm bằng Double Hashing để giảm thiểu rủi ro va chạm.
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public int countUniqueSubstrings(String s) {
        int n = s.length();
        long[] hash1 = new long[n + 1];
        long[] pow1 = new long[n + 1];
        long base1 = 31, mod1 = 1000000007;
        
        long[] hash2 = new long[n + 1];
        long[] pow2 = new long[n + 1];
        long base2 = 37, mod2 = 1000000009;

        pow1[0] = 1; pow2[0] = 1;
        for (int i = 0; i < n; i++) {
            hash1[i + 1] = (hash1[i] * base1 + s.charAt(i)) % mod1;
            pow1[i + 1] = (pow1[i] * base1) % mod1;
            
            hash2[i + 1] = (hash2[i] * base2 + s.charAt(i)) % mod2;
            pow2[i + 1] = (pow2[i] * base2) % mod2;
        }
        
        Set<Long> uniqueHashes = new HashSet<>();
        for (int len = 1; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                long h1 = (hash1[i + len] - hash1[i] * pow1[len] % mod1 + mod1) % mod1;
                long h2 = (hash2[i + len] - hash2[i] * pow2[len] % mod2 + mod2) % mod2;
                long combinedHash = (h1 << 32) | h2;
                uniqueHashes.add(combinedHash);
            }
        }
        return uniqueHashes.size();
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N^2)$ thời gian, $\mathcal{O}(N^2)$ không gian.

## 19. String to Integer (atoi) using Base Conversion
**Đề bài chi tiết:** Chuyển đổi một chuỗi văn bản chứa số (có thể có dấu, khoảng trắng) thành số nguyên 32-bit có dấu. Bài toán này là nền tảng của hàm Rolling Hash (cách cộng từng ký tự vào một biến tích lũy với một cơ số - base 10).
**Phân tích thuật toán:** Bỏ qua khoảng trắng đầu. Xác định dấu. Tính lũy tiến bằng cách lấy giá trị hiện tại nhân 10 cộng với chữ số mới. Kiểm tra tràn số (overflow) bằng cách so sánh trước khi nhân.
**Mã nguồn Java:**
```java
public class Solution {
    public int myAtoi(String s) {
        int i = 0, n = s.length();
        while (i < n && s.charAt(i) == ' ') i++;
        if (i == n) return 0;
        
        int sign = 1;
        if (s.charAt(i) == '+' || s.charAt(i) == '-') {
            sign = s.charAt(i) == '-' ? -1 : 1;
            i++;
        }
        
        int result = 0;
        while (i < n && Character.isDigit(s.charAt(i))) {
            int digit = s.charAt(i) - '0';
            
            if (result > Integer.MAX_VALUE / 10 || (result == Integer.MAX_VALUE / 10 && digit > 7)) {
                return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
            }
            
            result = result * 10 + digit;
            i++;
        }
        
        return result * sign;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N)$ thời gian, $\mathcal{O}(1)$ không gian.

## 20. Finding the Longest Common Prefix in O(1)
**Đề bài chi tiết:** Cho chuỗi $s$ và nhiều truy vấn, mỗi truy vấn gồm 2 chỉ số $i, j$. Cần trả về độ dài tiền tố chung dài nhất (LCP) của hai hậu tố bắt đầu tại $i$ và $j$ một cách nhanh chóng.
**Phân tích thuật toán:** Tính trước mảng băm tiền tố và mảng luỹ thừa (Rolling Hash). Để trả lời mỗi truy vấn, ta có thể dùng Binary Search tìm chiều dài tối đa mà mã băm của 2 chuỗi con bằng nhau. Chiều dài LCP lớn nhất nằm trong khoảng từ $0$ đến $\min(N-i, N-j)$.
**Mã nguồn Java:**
```java
public class Solution {
    long[] hash, pow;
    long base = 256, mod = 1000000007;
    int n;
    
    public void build(String s) {
        n = s.length();
        hash = new long[n + 1];
        pow = new long[n + 1];
        pow[0] = 1;
        for (int i = 0; i < n; i++) {
            hash[i + 1] = (hash[i] * base + s.charAt(i)) % mod;
            pow[i + 1] = (pow[i] * base) % mod;
        }
    }
    
    public int getLCP(int i, int j) {
        int left = 1, right = Math.min(n - i, n - j), ans = 0;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (getHash(i, i + mid) == getHash(j, j + mid)) {
                ans = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return ans;
    }
    
    private long getHash(int l, int r) {
        return (hash[r] - hash[l] * pow[r - l] % mod + mod) % mod;
    }
}
```
**Độ phức tạp:** Khởi tạo $\mathcal{O}(N)$, Truy vấn $\mathcal{O}(\log N)$ thời gian cho mỗi LCP. Không gian $\mathcal{O}(N)$.

## 21. Multi-Dimensional Rabin-Karp (Baker-Bird algorithm)
**Đề bài chi tiết:** Cho một ma trận văn bản $N \times N$ và một ma trận mẫu $M \times M$. Tìm tất cả các vị trí xuất hiện của ma trận mẫu trong ma trận văn bản.
**Phân tích thuật toán:** Mở rộng thuật toán Rolling Hash cho ma trận 2D. Thuật toán Baker-Bird tiêu chuẩn thường dùng Aho-Corasick trên các hàng/cột, nhưng ta có thể dùng Rabin-Karp 2 chiều: tính giá trị băm cho từng cột với độ dài $M$ tạo thành một ma trận hash 1D, sau đó tính hash theo hàng với độ dài $M$. Như vậy mỗi ma trận con $M \times M$ sẽ có một giá trị băm duy nhất có thể được tính trong $\mathcal{O}(1)$ thông qua Rolling Hash 2 chiều.
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public List<int[]> searchMatrix(int[][] text, int[][] pattern) {
        List<int[]> result = new ArrayList<>();
        int n = text.length;
        if (n == 0) return result;
        int m = pattern.length;
        if (m == 0 || m > n) return result;

        long base1 = 31, base2 = 37;
        long mod = 1000000007;

        long pHash = 0;
        for (int i = 0; i < m; i++) {
            long rowHash = 0;
            for (int j = 0; j < m; j++) {
                rowHash = (rowHash * base1 + pattern[i][j]) % mod;
            }
            pHash = (pHash * base2 + rowHash) % mod;
        }

        long pow1 = 1, pow2 = 1;
        for (int i = 0; i < m - 1; i++) pow1 = (pow1 * base1) % mod;
        for (int i = 0; i < m - 1; i++) pow2 = (pow2 * base2) % mod;

        long[][] rowHashes = new long[n][n - m + 1];
        for (int i = 0; i < n; i++) {
            long rHash = 0;
            for (int j = 0; j < m; j++) {
                rHash = (rHash * base1 + text[i][j]) % mod;
            }
            rowHashes[i][0] = rHash;
            for (int j = 1; j <= n - m; j++) {
                rHash = (rHash - text[i][j - 1] * pow1 % mod + mod) % mod;
                rHash = (rHash * base1 + text[i][j + m - 1]) % mod;
                rowHashes[i][j] = rHash;
            }
        }

        for (int j = 0; j <= n - m; j++) {
            long colHash = 0;
            for (int i = 0; i < m; i++) {
                colHash = (colHash * base2 + rowHashes[i][j]) % mod;
            }
            if (colHash == pHash) result.add(new int[]{0, j});

            for (int i = 1; i <= n - m; i++) {
                colHash = (colHash - rowHashes[i - 1][j] * pow2 % mod + mod) % mod;
                colHash = (colHash * base2 + rowHashes[i + m - 1][j]) % mod;
                if (colHash == pHash) result.add(new int[]{i, j});
            }
        }

        return result;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N^2)$ thời gian, $\mathcal{O}(N^2)$ không gian để lưu mảng `rowHashes`.

## 22. Check if Two Strings are Circularly Shifted
**Đề bài chi tiết:** Cho hai chuỗi $A$ và $B$. Kiểm tra xem chuỗi $A$ có phải là kết quả của việc dịch vòng (circular shift) chuỗi $B$ hay không. (Ví dụ: "abcde" và "cdeab").
**Phân tích thuật toán:** Một chuỗi $A$ là dịch vòng của $B$ nếu và chỉ nếu độ dài của chúng bằng nhau và $B$ là một chuỗi con của $A + A$. Ta có thể sử dụng thuật toán Rabin-Karp để tìm $B$ trong $A + A$. Điều này tiết kiệm thời gian so với việc thử xoay $A$ theo từng ký tự.
**Mã nguồn Java:**
```java
public class Solution {
    public boolean isCircularShift(String A, String B) {
        if (A.length() != B.length()) return false;
        if (A.length() == 0) return true;

        String text = A + A;
        String pattern = B;
        long pHash = 0, tHash = 0;
        long base = 256, mod = 1000000007;
        long h = 1;
        int m = pattern.length(), n = text.length();

        for (int i = 0; i < m - 1; i++) h = (h * base) % mod;

        for (int i = 0; i < m; i++) {
            pHash = (pHash * base + pattern.charAt(i)) % mod;
            tHash = (tHash * base + text.charAt(i)) % mod;
        }

        for (int i = 0; i <= n - m; i++) {
            if (pHash == tHash) {
                if (text.substring(i, i + m).equals(pattern)) return true;
            }
            if (i < n - m) {
                tHash = (base * (tHash - text.charAt(i) * h) + text.charAt(i + m)) % mod;
                if (tHash < 0) tHash += mod;
            }
        }
        return false;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N)$ thời gian, $\mathcal{O}(N)$ không gian để tạo chuỗi $A + A$.

## 23. Prefix and Suffix Search
**Đề bài chi tiết:** Thiết kế một cấu trúc dữ liệu lưu trữ một danh sách các từ với trọng số (là chỉ số của chúng trong danh sách ban đầu). Cấu trúc cần hỗ trợ truy vấn trả về trọng số lớn nhất của một từ có cả tiền tố và hậu tố cho trước.
**Phân tích thuật toán:** Mỗi từ trong từ điển được biến đổi thành nhiều mẫu có dạng `suffix + "#" + prefix`. Với mỗi cặp tiền tố và hậu tố có thể có của từ, ta sinh ra chuỗi kết hợp và tính mã băm (hoặc đưa vào HashMap String). Khi truy vấn, ta chỉ cần tìm kiếm `suffix + "#" + prefix` trong HashMap để trả về chỉ số lớn nhất được lưu.
**Mã nguồn Java:**
```java
import java.util.*;

public class WordFilter {
    Map<String, Integer> map;

    public WordFilter(String[] words) {
        map = new HashMap<>();
        for (int w = 0; w < words.length; w++) {
            String word = words[w];
            int n = word.length();
            for (int i = 0; i <= n; i++) {
                String prefix = word.substring(0, i);
                for (int j = 0; j <= n; j++) {
                    String suffix = word.substring(j, n);
                    map.put(suffix + "#" + prefix, w);
                }
            }
        }
    }

    public int f(String pref, String suff) {
        String key = suff + "#" + pref;
        return map.getOrDefault(key, -1);
    }
}
```
**Độ phức tạp:** Khởi tạo $\mathcal{O}(N \times L^3)$ thời gian với $N$ là số từ và $L$ là độ dài tối đa (do lệnh substring), Truy vấn $\mathcal{O}(L_p + L_s)$ thời gian để tạo key. (Lưu ý: Có thể tối ưu thành $\mathcal{O}(N \times L^2)$ nếu tự tính Hash hoặc dùng Trie kết hợp).

## 24. Implement String.contains() for Unicode Text
**Đề bài chi tiết:** Cài đặt thuật toán tìm kiếm chuỗi (ví dụ: `String.contains()`) áp dụng trên mảng byte đại diện cho văn bản Unicode (ví dụ UTF-8).
**Phân tích thuật toán:** Thay vì chuyển đổi sang mảng `char` hoặc `String`, ta có thể xử lý trực tiếp mảng byte UTF-8 bằng thuật toán Rabin-Karp. Cơ số được chọn có thể là 256. Bất kỳ ký tự Unicode nào chiếm nhiều byte vẫn sẽ được coi như một chuỗi các byte liên tiếp, do đó không cần phải giải mã thành ký tự, giúp thuật toán chạy rất nhanh trên byte buffer nguyên bản.
**Mã nguồn Java:**
```java
public class Solution {
    public boolean contains(byte[] text, byte[] pattern) {
        if (pattern.length == 0) return true;
        if (text.length < pattern.length) return false;

        long pHash = 0, tHash = 0, h = 1;
        long base = 256, mod = 1000000007;
        int m = pattern.length, n = text.length;

        for (int i = 0; i < m - 1; i++) h = (h * base) % mod;

        for (int i = 0; i < m; i++) {
            pHash = (pHash * base + (pattern[i] & 0xFF)) % mod;
            tHash = (tHash * base + (text[i] & 0xFF)) % mod;
        }

        for (int i = 0; i <= n - m; i++) {
            if (pHash == tHash) {
                boolean match = true;
                for (int j = 0; j < m; j++) {
                    if (text[i + j] != pattern[j]) {
                        match = false;
                        break;
                    }
                }
                if (match) return true;
            }
            if (i < n - m) {
                tHash = (base * (tHash - (text[i] & 0xFF) * h) + (text[i + m] & 0xFF)) % mod;
                if (tHash < 0) tHash += mod;
            }
        }
        return false;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N + M)$ thời gian trên số byte, $\mathcal{O}(1)$ không gian.

## 25. DNA Sequence Mutation Finder
**Đề bài chi tiết:** Tìm tất cả các chuỗi con DNA (chỉ gồm A, C, G, T) có độ dài 10 trong một chuỗi lớn, sao cho chúng khớp với một mẫu DNA cho trước với nhiều nhất 1 ký tự khác biệt (tương đương khoảng cách Hamming $\le 1$).
**Phân tích thuật toán:** Tính Hash cho mẫu gốc và tất cả các đột biến có thể có của mẫu đó (thay đổi đúng 1 vị trí với 3 loại nucleotide còn lại). Tổng cộng có $10 \times 3 = 30$ đột biến. Sau đó đưa giá trị băm của chúng vào một HashSet. Tiếp theo, dùng Sliding Window kết hợp với thuật toán Rabin-Karp để duyệt qua chuỗi lớn, kiểm tra xem mã băm hiện tại có nằm trong HashSet hay không.
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public List<Integer> findMutations(String s, String pattern) {
        List<Integer> result = new ArrayList<>();
        if (s.length() < pattern.length()) return result;

        int m = pattern.length();
        long base = 4, mod = 1000000007;
        Set<Long> mutatedHashes = new HashSet<>();
        
        char[] dna = {'A', 'C', 'G', 'T'};
        for (int i = 0; i < m; i++) {
            char original = pattern.charAt(i);
            for (char c : dna) {
                if (c == original) {
                    mutatedHashes.add(getHash(pattern, base, mod));
                } else {
                    String mutated = pattern.substring(0, i) + c + pattern.substring(i + 1);
                    mutatedHashes.add(getHash(mutated, base, mod));
                }
            }
        }

        long tHash = 0, h = 1;
        for (int i = 0; i < m - 1; i++) h = (h * base) % mod;

        for (int i = 0; i < s.length(); i++) {
            tHash = (tHash * base + mapVal(s.charAt(i))) % mod;
            if (i >= m) {
                tHash = (tHash - mapVal(s.charAt(i - m)) * h % mod + mod) % mod;
            }
            if (i >= m - 1 && mutatedHashes.contains(tHash)) {
                result.add(i - m + 1);
            }
        }
        return result;
    }

    private long getHash(String s, long base, long mod) {
        long hash = 0;
        for (char c : s.toCharArray()) hash = (hash * base + mapVal(c)) % mod;
        return hash;
    }

    private int mapVal(char c) {
        if (c == 'A') return 0;
        if (c == 'C') return 1;
        if (c == 'G') return 2;
        return 3;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N + M^2)$ thời gian với $M=10$ là hằng số, $\mathcal{O}(M)$ không gian cho HashSet.

## 26. Password Pattern Matching
**Đề bài chi tiết:** Bạn có một luồng văn bản (độ dài rất lớn) và một danh sách 10,000 mật khẩu bị rò rỉ. Tìm xem trong văn bản có chứa mật khẩu rò rỉ nào hay không.
**Phân tích thuật toán:** Với số lượng mẫu (pattern) lớn, Aho-Corasick là giải pháp tối ưu nhất. Tuy nhiên ta hoàn toàn có thể dùng Rabin-Karp trong bài toán Multiple Pattern Matching: Gom nhóm các mật khẩu theo độ dài. Tính Rolling Hash trên luồng văn bản cho nhiều độ dài cửa sổ cùng một lúc, sau đó đối chiếu mã băm vào HashSet của độ dài tương ứng.
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public boolean containsLeakedPassword(String text, String[] passwords) {
        Map<Integer, Set<Long>> lengthToHashes = new HashMap<>();
        long base = 256, mod = 1000000007;

        for (String p : passwords) {
            int len = p.length();
            long h = 0;
            for (char c : p.toCharArray()) h = (h * base + c) % mod;
            lengthToHashes.putIfAbsent(len, new HashSet<>());
            lengthToHashes.get(len).add(h);
        }

        for (int len : lengthToHashes.keySet()) {
            if (text.length() < len) continue;
            long tHash = 0, h = 1;
            for (int i = 0; i < len - 1; i++) h = (h * base) % mod;

            for (int i = 0; i < text.length(); i++) {
                tHash = (tHash * base + text.charAt(i)) % mod;
                if (i >= len) {
                    tHash = (tHash - text.charAt(i - len) * h % mod + mod) % mod;
                }
                if (i >= len - 1) {
                    if (lengthToHashes.get(len).contains(tHash)) {
                        // Cần so sánh chuỗi thực tế để tránh hash collision
                        // Giả định bỏ qua trong đoạn mã mẫu này.
                        return true;
                    }
                }
            }
        }
        return false;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(K \times N)$ với $K$ là số lượng độ dài phân biệt của mật khẩu, $\mathcal{O}(P)$ không gian để lưu mã băm của tất cả mật khẩu.

## 27. Minimum Window Substring Using Hashing
**Đề bài chi tiết:** Cải tiến thuật toán Sliding Window cho bài toán "Minimum Window Substring" (tìm chuỗi con ngắn nhất trong $S$ chứa mọi ký tự của $T$ với số lượng tương ứng).
**Phân tích thuật toán:** Bài toán gốc sử dụng mảng đếm tần số ký tự (như một hash array). Bằng cách duyệt qua các ký tự, ta duy trì biến đếm để so sánh xem cửa sổ hiện tại đã bao phủ hoàn toàn chuỗi $T$ chưa. Một biến đổi tinh tế là dùng giá trị băm tần số, tuy nhiên cách đếm mảng chuẩn vẫn là phương pháp Hashing ở mức ký tự nhanh nhất.
**Mã nguồn Java:**
```java
public class Solution {
    public String minWindow(String s, String t) {
        if (s.length() == 0 || t.length() == 0) return "";
        int[] hash = new int[256];
        for (char c : t.toCharArray()) hash[c]++;
        
        int left = 0, count = 0, minLen = Integer.MAX_VALUE, minLeft = 0;
        for (int right = 0; right < s.length(); right++) {
            char c = s.charAt(right);
            if (hash[c] > 0) count++;
            hash[c]--;
            
            while (count == t.length()) {
                if (right - left + 1 < minLen) {
                    minLen = right - left + 1;
                    minLeft = left;
                }
                char leftChar = s.charAt(left);
                hash[leftChar]++;
                if (hash[leftChar] > 0) count--;
                left++;
            }
        }
        return minLen == Integer.MAX_VALUE ? "" : s.substring(minLeft, minLeft + minLen);
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N)$ thời gian, $\mathcal{O}(1)$ không gian (sử dụng bảng mã ASCII 256 ký tự).

## 28. LZW Compression Simulation
**Đề bài chi tiết:** Mô phỏng thuật toán nén LZW: Xây dựng một từ điển các chuỗi đã gặp, liên tục tìm chuỗi con dài nhất khớp với một mục trong từ điển để thay thế bằng mã tương ứng, sau đó đẩy một mã mới của chuỗi dài hơn vào từ điển.
**Phân tích thuật toán:** Thuật toán dùng bộ băm (HashMap) để tra cứu và lưu trữ chuỗi trong từ điển với chi phí $\mathcal{O}(1)$. Thay vì dùng HashMap String bình thường có thể tốn kém bộ nhớ, Hash-based Trie hoặc dùng giá trị băm nguyên của Rolling Hash có thể tối ưu hoá quá trình mô phỏng nén này.
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public List<Integer> lzwCompress(String text) {
        Map<String, Integer> dictionary = new HashMap<>();
        for (int i = 0; i < 256; i++) {
            dictionary.put(String.valueOf((char) i), i);
        }
        
        String p = "";
        List<Integer> result = new ArrayList<>();
        int code = 256;
        
        for (char c : text.toCharArray()) {
            String pc = p + c;
            if (dictionary.containsKey(pc)) {
                p = pc;
            } else {
                result.add(dictionary.get(p));
                dictionary.put(pc, code++);
                p = String.valueOf(c);
            }
        }
        if (!p.equals("")) {
            result.add(dictionary.get(p));
        }
        return result;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N \times L)$ trung bình với Hash Map, trong đó $L$ là độ dài tối đa của một từ vựng, $\mathcal{O}(V)$ không gian từ điển.

## 29. Find Anagrams with Rolling Hash
**Đề bài chi tiết:** Cho chuỗi $s$ và chuỗi $p$. Tìm tất cả chỉ số bắt đầu của các chuỗi con trong $s$ là phép hoán vị (anagrams) của $p$.
**Phân tích thuật toán:** Một dạng mã băm không quan tâm thứ tự (Order-independent hashing). Ta có thể gán mỗi ký tự 'a'-'z' với một số nguyên tố ngẫu nhiên, mã băm của một chuỗi là tích các mã của từng ký tự modulo một số nguyên tố lớn. Nhờ đó, thao tác Rolling Hash (nhân và chia nghịch đảo) cho phép kiểm tra độ khớp của dãy Anagram trong $\mathcal{O}(1)$. Dưới đây là cách mô phỏng dùng Sliding Window và mảng tần số (một biến thể Hash tần số an toàn tuyệt đối).
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        List<Integer> result = new ArrayList<>();
        if (s.length() < p.length()) return result;
        
        int[] pCount = new int[26];
        int[] sCount = new int[26];
        int m = p.length();
        
        for (char c : p.toCharArray()) pCount[c - 'a']++;
        
        for (int i = 0; i < s.length(); i++) {
            sCount[s.charAt(i) - 'a']++;
            if (i >= m) {
                sCount[s.charAt(i - m) - 'a']--;
            }
            if (i >= m - 1 && Arrays.equals(pCount, sCount)) {
                result.add(i - m + 1);
            }
        }
        
        return result;
    }
}
```
**Độ phức tạp:** $\mathcal{O}(N)$ thời gian, $\mathcal{O}(1)$ không gian (chỉ dùng các mảng kích thước cố định 26).

## 30. Plagiarism Checker Core (Winnowing Algorithm)
**Đề bài chi tiết:** Viết lõi phần mềm phát hiện đạo văn sử dụng thuật toán Winnowing. Chia văn bản thành các $k$-gram, tính mã băm cho từng $k$-gram (bằng Rolling Hash). Sau đó tạo ra một tập hợp fingerprint thu gọn bằng cách chọn mã băm nhỏ nhất trong từng cửa sổ trượt kích thước $w$. Cuối cùng, trả về tỷ lệ độ tương đồng (tập giao) của các fingerprints giữa hai tài liệu.
**Phân tích thuật toán:** Bước 1: Tính mã băm của mọi chuỗi con độ dài $k$ (Sử dụng Rabin-Karp). Bước 2: Dùng thuật toán Sliding Window Minimum (Sử dụng hàng đợi hai đầu Monotonic Deque) trên cửa sổ cỡ $w$ để chọn fingerprint tối ưu. Bước 3: Đếm số lượng điểm chung thông qua HashSet.
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    public Set<Long> getFingerprints(String text, int k, int w) {
        if (text.length() < k) return new HashSet<>();
        long base = 256, mod = 1000000007;
        long[] hashes = new long[text.length() - k + 1];
        long h = 0, pow = 1;
        
        for (int i = 0; i < k - 1; i++) pow = (pow * base) % mod;
        
        for (int i = 0; i < text.length(); i++) {
            h = (h * base + text.charAt(i)) % mod;
            if (i >= k) h = (h - text.charAt(i - k) * pow % mod + mod) % mod;
            if (i >= k - 1) hashes[i - k + 1] = h;
        }
        
        Set<Long> fingerprints = new HashSet<>();
        Deque<Integer> dq = new LinkedList<>();
        for (int i = 0; i < hashes.length; i++) {
            if (!dq.isEmpty() && dq.peekFirst() < i - w + 1) dq.pollFirst();
            while (!dq.isEmpty() && hashes[dq.peekLast()] >= hashes[i]) dq.pollLast();
            dq.offerLast(i);
            if (i >= w - 1) fingerprints.add(hashes[dq.peekFirst()]);
        }
        return fingerprints;
    }
    
    public double similarity(String doc1, String doc2, int k, int w) {
        Set<Long> fp1 = getFingerprints(doc1, k, w);
        Set<Long> fp2 = getFingerprints(doc2, k, w);
        int common = 0;
        for (long hash : fp1) {
            if (fp2.contains(hash)) common++;
        }
        if (fp1.isEmpty() && fp2.isEmpty()) return 1.0;
        return (double) common / Math.max(fp1.size(), fp2.size());
    }
}
```
**Độ phức tạp:** Tính Hash $\mathcal{O}(N)$, Chọn Fingerprint bằng Deque $\mathcal{O}(N)$, Lưu vào HashSet $\mathcal{O}(N/W)$ không gian.

