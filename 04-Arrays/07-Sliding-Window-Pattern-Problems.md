# Companion Problems: 07-Sliding-Window-Pattern

Tài liệu này cung cấp **30 bài tập thực hành** thao tác với thuật toán Sliding Window (Cửa sổ trượt).

---

## 1. Maximum Average Subarray I (LeetCode 643)
**Đề bài chi tiết:** Cho mảng `nums` và số nguyên `k`. Tìm mảng con liên tiếp có độ dài đúng bằng `k` có trung bình cộng lớn nhất.
**Phân tích thuật toán:** Fixed-size window. Khởi tạo tổng `k` phần tử đầu tiên. Trượt cửa sổ: `sum = sum + nums[right] - nums[right - k]`. Cập nhật `maxSum`.
**Mã nguồn Java:**
```java
public class MaxAverageSubarray {
    public double findMaxAverage(int[] nums, int k) {
        long sum = 0;
        for (int i = 0; i < k; i++) sum += nums[i];
        
        long maxSum = sum;
        for (int i = k; i < nums.length; i++) {
            sum += nums[i] - nums[i - k];
            maxSum = Math.max(maxSum, sum);
        }
        
        return (double) maxSum / k;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 2. Minimum Size Subarray Sum (LeetCode 209)
**Đề bài chi tiết:** Cho mảng số nguyên dương `nums` và số `target`. Tìm chiều dài NHỎ NHẤT của mảng con có tổng $\ge target$.
**Phân tích thuật toán:** Variable-size window (Tìm min). Thêm dần phần tử vào `sum`. Khi `sum >= target`, ghi nhận `minLength` và bắt đầu thu hẹp `left` để tìm mảng ngắn hơn.
**Mã nguồn Java:**
```java
public class MinSizeSubarray {
    public int minSubArrayLen(int target, int[] nums) {
        int left = 0;
        int minLen = Integer.MAX_VALUE;
        int sum = 0;
        
        for (int right = 0; right < nums.length; right++) {
            sum += nums[right];
            
            while (sum >= target) {
                minLen = Math.min(minLen, right - left + 1);
                sum -= nums[left];
                left++;
            }
        }
        
        return minLen == Integer.MAX_VALUE ? 0 : minLen;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 3. Longest Substring Without Repeating Characters (LeetCode 3)
**Đề bài chi tiết:** Tìm chiều dài chuỗi con dài nhất không chứa ký tự lặp lại.
**Phân tích thuật toán:** Variable-size window (Tìm max). Dùng mảng `boolean` hoặc `int[]` để đánh dấu ký tự đã xuất hiện. Nếu ký tự ở `right` đã có trong window, tiến `left` và xóa ký tự khỏi map cho đến khi hết lặp.
**Mã nguồn Java:**
```java
public class LongestSubstring {
    public int lengthOfLongestSubstring(String s) {
        boolean[] seen = new boolean[256];
        int left = 0, maxLen = 0;
        
        for (int right = 0; right < s.length(); right++) {
            char c = s.charAt(right);
            
            while (seen[c]) {
                seen[s.charAt(left)] = false;
                left++;
            }
            
            seen[c] = true;
            maxLen = Math.max(maxLen, right - left + 1);
        }
        
        return maxLen;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$ (Bộ đếm 256 chars).

---

## 4. Longest Repeating Character Replacement (LeetCode 424)
**Đề bài chi tiết:** Cho chuỗi toàn chữ in hoa. Bạn có thể thay đổi tối đa $K$ ký tự. Tìm chuỗi con dài nhất chỉ chứa cùng 1 ký tự.
**Phân tích thuật toán:** Tìm `maxCount` (số lần xuất hiện của ký tự nhiều nhất trong window hiện tại). Số ký tự cần phải đổi trong window là `length - maxCount`. Nếu `length - maxCount > K`, window vi phạm $\rightarrow$ tiến `left`. (Trick: `maxCount` không cần giảm khi `left` tiến vì ta chỉ quan tâm kỷ lục lớn hơn).
**Mã nguồn Java:**
```java
public class CharacterReplacement {
    public int characterReplacement(String s, int k) {
        int[] count = new int[26];
        int left = 0, maxLen = 0, maxCount = 0;
        
        for (int right = 0; right < s.length(); right++) {
            int c = s.charAt(right) - 'A';
            count[c]++;
            maxCount = Math.max(maxCount, count[c]);
            
            // Cửa sổ vi phạm
            if (right - left + 1 - maxCount > k) {
                count[s.charAt(left) - 'A']--;
                left++;
            }
            
            maxLen = Math.max(maxLen, right - left + 1);
        }
        
        return maxLen;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$ (Mảng 26).

---

## 5. Permutation in String (LeetCode 567)
**Đề bài chi tiết:** Trả về true nếu `s2` chứa hoán vị của `s1`. (Nói cách khác, `s2` chứa một substring có tập ký tự giống hệt `s1`).
**Phân tích thuật toán:** Fixed-size window với độ dài $K = s1.length()$. Đếm tần suất các ký tự của `s1` vào mảng `map1`. Dùng mảng `map2` để đếm ký tự trong cửa sổ trượt trên `s2`. Nếu hai mảng bằng nhau (Arrays.equals) $\rightarrow$ Trả về true.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class PermutationInString {
    public boolean checkInclusion(String s1, String s2) {
        if (s1.length() > s2.length()) return false;
        
        int[] map1 = new int[26];
        int[] map2 = new int[26];
        
        for (int i = 0; i < s1.length(); i++) {
            map1[s1.charAt(i) - 'a']++;
            map2[s2.charAt(i) - 'a']++;
        }
        
        if (Arrays.equals(map1, map2)) return true;
        
        for (int right = s1.length(); right < s2.length(); right++) {
            map2[s2.charAt(right) - 'a']++;
            map2[s2.charAt(right - s1.length()) - 'a']--;
            
            if (Arrays.equals(map1, map2)) return true;
        }
        
        return false;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$ (Do Arrays.equals kiểm tra mảng nhỏ 26 phần tử).

---

## 6. Max Consecutive Ones III (LeetCode 1004)
**Đề bài chi tiết:** Cho mảng nhị phân. Lật tối đa K số 0 thành 1. Tìm chuỗi số 1 dài nhất.
**Phân tích thuật toán:** Bài toán tương đương: Tìm Subarray dài nhất chứa tối đa K số 0. Variable-size window. Nếu đếm số 0 `> K`, trượt `left` và trừ biến đếm số 0 cho đến khi hợp lệ.
**Mã nguồn Java:**
```java
public class MaxConsecutiveOnesIII {
    public int longestOnes(int[] nums, int k) {
        int left = 0, zeroCount = 0;
        int maxLen = 0;
        
        for (int right = 0; right < nums.length; right++) {
            if (nums[right] == 0) zeroCount++;
            
            while (zeroCount > k) {
                if (nums[left] == 0) zeroCount--;
                left++;
            }
            
            maxLen = Math.max(maxLen, right - left + 1);
        }
        
        return maxLen;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 7. Fruit Into Baskets (LeetCode 904)
**Đề bài chi tiết:** Cho mảng các loại trái cây. Bạn có 2 giỏ, mỗi giỏ chỉ chứa được 1 LOẠI trái cây (không giới hạn số lượng). Thu thập dãy trái cây dài nhất.
**Phân tích thuật toán:** Bài toán tương đương: Tìm Longest Subarray chứa tối đa 2 SỐ KHÁC NHAU. Dùng HashMap để đếm tần suất. Khi `map.size() > 2`, tiến `left` và loại dần trái cây cũ ra khỏi map.
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class FruitIntoBaskets {
    public int totalFruit(int[] fruits) {
        HashMap<Integer, Integer> map = new HashMap<>();
        int left = 0, maxLen = 0;
        
        for (int right = 0; right < fruits.length; right++) {
            map.put(fruits[right], map.getOrDefault(fruits[right], 0) + 1);
            
            while (map.size() > 2) {
                map.put(fruits[left], map.get(fruits[left]) - 1);
                if (map.get(fruits[left]) == 0) {
                    map.remove(fruits[left]);
                }
                left++;
            }
            
            maxLen = Math.max(maxLen, right - left + 1);
        }
        
        return maxLen;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$ (Map tối đa chứa 3 phần tử).

---

## 8. Find All Anagrams in a String (LeetCode 438)
**Đề bài chi tiết:** Tìm tất cả các chỉ số bắt đầu của mảng con (substring) trong `s` là một Anagram của `p`.
**Phân tích thuật toán:** Hoàn toàn tương tự Bài 5 (Permutation in String), nhưng thay vì return true, ta lưu `right - p.length() + 1` (Chỉ số left) vào List kết quả.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class FindAnagrams {
    public List<Integer> findAnagrams(String s, String p) {
        List<Integer> res = new ArrayList<>();
        if (s.length() < p.length()) return res;
        
        int[] pCount = new int[26];
        int[] sCount = new int[26];
        int k = p.length();
        
        for (int i = 0; i < k; i++) {
            pCount[p.charAt(i) - 'a']++;
            sCount[s.charAt(i) - 'a']++;
        }
        
        if (Arrays.equals(pCount, sCount)) res.add(0);
        
        for (int right = k; right < s.length(); right++) {
            sCount[s.charAt(right) - 'a']++;
            sCount[s.charAt(right - k) - 'a']--;
            
            if (Arrays.equals(pCount, sCount)) {
                res.add(right - k + 1);
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 9. Sliding Window Maximum (LeetCode 239)
**Đề bài chi tiết:** Trả về mảng chứa phần tử Lớn nhất của mỗi cửa sổ trượt kích thước $K$.
**Phân tích thuật toán:** Đây là bài tủ. Sử dụng Monotonic Deque (Hàng đợi hai đầu đơn điệu). Deque lưu CHỈ SỐ. Duy trì Deque giảm dần. Mỗi bước: (1) Bỏ phần tử cũ khỏi đầu Deque. (2) Đuổi các phần tử nhỏ hơn phần tử mới ở đuôi Deque. (3) Thêm phần tử mới vào. Số ở đầu Deque luôn là chỉ số của Max.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class SlidingWindowMax {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if (nums == null || k <= 0) return new int[0];
        int[] res = new int[nums.length - k + 1];
        Deque<Integer> deque = new ArrayDeque<>();
        
        for (int i = 0; i < nums.length; i++) {
            // Loại bỏ chỉ số đã trượt ra ngoài Window
            if (!deque.isEmpty() && deque.peekFirst() == i - k) {
                deque.pollFirst();
            }
            
            // Loại bỏ các phần tử nhỏ hơn phần tử mới ở Đuôi
            while (!deque.isEmpty() && nums[deque.peekLast()] < nums[i]) {
                deque.pollLast();
            }
            
            deque.offerLast(i);
            
            // Bắt đầu ghi kết quả khi Window đạt cỡ K
            if (i >= k - 1) {
                res[i - k + 1] = nums[deque.peekFirst()];
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$ (Mỗi phần tử push và pop tối đa 1 lần), Space $\mathcal{O}(K)$.

---

## 10. Minimum Window Substring (LeetCode 76)
**Đề bài chi tiết:** Cho chuỗi $S$ và $T$. Tìm Substring ngắn nhất của $S$ chứa mọi ký tự của $T$ (kể cả lặp lại).
**Phân tích thuật toán:** Variable-size window (Shortest). Khởi tạo mảng đếm tần suất của $T$. Biến `matchCount` đếm số lượng kí tự đã gom đủ. Mở rộng `right` để gom đủ `matchCount == T.length()`. Khi đã đủ, bắt đầu tiến `left` để thu hẹp nhằm tìm Substring ngắn hơn. Tại mỗi vòng lặp hợp lệ, lưu trữ index để tạo chuỗi lúc sau.
**Mã nguồn Java:**
```java
public class MinWindowSubstring {
    public String minWindow(String s, String t) {
        int[] tMap = new int[128];
        for (char c : t.toCharArray()) tMap[c]++;
        
        int left = 0, minLeft = 0, minLen = Integer.MAX_VALUE;
        int matchCount = 0;
        
        for (int right = 0; right < s.length(); right++) {
            char rChar = s.charAt(right);
            if (tMap[rChar] > 0) matchCount++; // Đóng góp có ích
            tMap[rChar]--; // Vẫn trừ bình thường
            
            while (matchCount == t.length()) { // Đã gom đủ ký tự
                if (right - left + 1 < minLen) {
                    minLen = right - left + 1;
                    minLeft = left;
                }
                
                char lChar = s.charAt(left);
                tMap[lChar]++; // Trả lại
                if (tMap[lChar] > 0) matchCount--; // Mất đi 1 ký tự quan trọng
                left++;
            }
        }
        
        return minLen == Integer.MAX_VALUE ? "" : s.substring(minLeft, minLeft + minLen);
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$ (Mảng 128 chars).

---

## 11. Subarrays with K Different Integers (LeetCode 992)
**Đề bài chi tiết:** Cho một mảng số nguyên `nums` và một số nguyên `k`. Một mảng con được gọi là tốt (good) nếu số lượng các số nguyên khác nhau trong mảng con đó chính xác bằng `k`. Trả về số lượng các mảng con tốt của `nums`.
**Phân tích thuật toán:** Bài toán đếm số mảng con có chính xác `K` phần tử khác nhau có thể được giải bằng cách lấy số mảng con có "tối đa `K` phần tử khác nhau" trừ đi số mảng con có "tối đa `K-1` phần tử khác nhau" (Hàm `atMost(K) - atMost(K-1)`). Hàm `atMost(k)` sử dụng Variable-size Window với một mảng đếm (hoặc HashMap) để duy trì số lượng phần tử phân biệt $\le k$.
**Mã nguồn Java:**
```java
public class SubarraysWithKDifferent {
    public int subarraysWithKDistinct(int[] nums, int k) {
        return atMost(nums, k) - atMost(nums, k - 1);
    }
    
    private int atMost(int[] nums, int k) {
        int[] count = new int[nums.length + 1];
        int left = 0, res = 0, distinct = 0;
        
        for (int right = 0; right < nums.length; right++) {
            if (count[nums[right]] == 0) distinct++;
            count[nums[right]]++;
            
            while (distinct > k) {
                count[nums[left]]--;
                if (count[nums[left]] == 0) distinct--;
                left++;
            }
            // Mọi mảng con kết thúc tại right và bắt đầu từ một chỉ số trong [left, right]
            // đều có số phần tử khác biệt <= k. Số lượng là right - left + 1.
            res += right - left + 1;
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 12. Grumpy Bookstore Owner (LeetCode 1052)
**Đề bài chi tiết:** Một chủ hiệu sách có mảng `customers` (số lượng khách) và `grumpy` (0: vui vẻ, 1: cáu gắt) theo từng phút. Có thể sử dụng một kĩ thuật để giữ chủ hiệu sách vui vẻ trong `minutes` phút liên tiếp. Tìm số lượng khách hàng tối đa cảm thấy hài lòng (không bị cáu gắt).
**Phân tích thuật toán:** Tính trước tổng số khách hàng tự nhiên cảm thấy hài lòng khi chủ hiệu sách không cáu gắt (`grumpy[i] == 0`). Kĩ thuật giữ vui vẻ trong `minutes` phút liên tiếp chính là tìm một Fixed-size Window có độ dài `minutes` sao cho tổng số khách hàng **có thể cứu được** (khi `grumpy[i] == 1`) là lớn nhất. Cộng kết quả này với tổng tự nhiên.
**Mã nguồn Java:**
```java
public class GrumpyBookstoreOwner {
    public int maxSatisfied(int[] customers, int[] grumpy, int minutes) {
        int satisfied = 0;
        for (int i = 0; i < customers.length; i++) {
            if (grumpy[i] == 0) satisfied += customers[i];
        }
        
        int maxExtra = 0;
        int currentExtra = 0;
        
        for (int i = 0; i < customers.length; i++) {
            // Thêm khách hàng vào cửa sổ
            if (grumpy[i] == 1) currentExtra += customers[i];
            
            // Bỏ khách hàng ra khỏi cửa sổ (khi vượt quá minutes)
            if (i >= minutes) {
                if (grumpy[i - minutes] == 1) {
                    currentExtra -= customers[i - minutes];
                }
            }
            
            maxExtra = Math.max(maxExtra, currentExtra);
        }
        
        return satisfied + maxExtra;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 13. Substring with Concatenation of All Words (LeetCode 30)
**Đề bài chi tiết:** Cho chuỗi `s` và mảng `words` chứa các từ có **cùng độ dài**. Tìm tất cả các chỉ số bắt đầu của chuỗi con trong `s` là sự nối tiếp của tất cả các từ trong `words` theo bất kỳ thứ tự nào, không thừa phần tử nào.
**Phân tích thuật toán:** Gọi độ dài mỗi từ là `L`. Cửa sổ trượt có kích thước là `words.length * L`. Có thể chia bài toán thành `L` trường hợp duyệt bắt đầu từ các vị trí từ `0` đến `L-1`. Với mỗi vị trí, sử dụng Sliding Window trượt theo từng bước `L`. Dùng HashMap đếm số lượng từng từ trong `words`, và một HashMap thứ hai cho cửa sổ hiện tại.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class SubstringConcatenation {
    public List<Integer> findSubstring(String s, String[] words) {
        List<Integer> res = new ArrayList<>();
        if (s == null || words == null || words.length == 0) return res;
        
        int wordLen = words[0].length();
        int numWords = words.length;
        Map<String, Integer> wordCount = new HashMap<>();
        
        for (String word : words) {
            wordCount.put(word, wordCount.getOrDefault(word, 0) + 1);
        }
        
        for (int i = 0; i < wordLen; i++) {
            int left = i, count = 0;
            Map<String, Integer> seen = new HashMap<>();
            
            for (int right = i; right <= s.length() - wordLen; right += wordLen) {
                String sub = s.substring(right, right + wordLen);
                if (wordCount.containsKey(sub)) {
                    seen.put(sub, seen.getOrDefault(sub, 0) + 1);
                    count++;
                    
                    while (seen.get(sub) > wordCount.get(sub)) {
                        String leftWord = s.substring(left, left + wordLen);
                        seen.put(leftWord, seen.get(leftWord) - 1);
                        count--;
                        left += wordLen;
                    }
                    
                    if (count == numWords) {
                        res.add(left);
                    }
                } else {
                    seen.clear();
                    count = 0;
                    left = right + wordLen;
                }
            }
        }
        
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \times L)$, Space $\mathcal{O}(M)$ (Với N là chiều dài S, L là độ dài từ, M là tổng số từ).

---

## 14. Longest Substring with At Least K Repeating Characters (LeetCode 395)
**Đề bài chi tiết:** Cho chuỗi `s` và số nguyên `k`. Tìm độ dài của chuỗi con dài nhất mà trong đó tần suất xuất hiện của **mỗi** ký tự thuộc chuỗi con đó đều $\ge k$.
**Phân tích thuật toán:** Mặc dù bài này thường giải bằng Divide & Conquer, có thể áp dụng Sliding Window bằng cách lặp số lượng ký tự khác biệt (từ 1 đến 26). Với mỗi số lượng unique ký tự `M`, ta dùng Variable-size Window để tìm chuỗi con dài nhất chứa đúng `M` ký tự khác nhau, và trong đó mọi ký tự xuất hiện ít nhất `k` lần.
**Mã nguồn Java:**
```java
public class LongestSubstringKRepeating {
    public int longestSubstring(String s, int k) {
        int maxLen = 0;
        for (int uniqueTarget = 1; uniqueTarget <= 26; uniqueTarget++) {
            int[] counts = new int[26];
            int left = 0, unique = 0, countAtLeastK = 0;
            
            for (int right = 0; right < s.length(); right++) {
                int cRight = s.charAt(right) - 'a';
                if (counts[cRight] == 0) unique++;
                counts[cRight]++;
                if (counts[cRight] == k) countAtLeastK++;
                
                while (unique > uniqueTarget) {
                    int cLeft = s.charAt(left) - 'a';
                    if (counts[cLeft] == k) countAtLeastK--;
                    counts[cLeft]--;
                    if (counts[cLeft] == 0) unique--;
                    left++;
                }
                
                if (unique == uniqueTarget && unique == countAtLeastK) {
                    maxLen = Math.max(maxLen, right - left + 1);
                }
            }
        }
        return maxLen;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(26 \times N) = \mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 15. Contains Duplicate II (LeetCode 219)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` và số nguyên `k`. Kiểm tra xem có hai chỉ số `i` và `j` phân biệt sao cho `nums[i] == nums[j]` và `abs(i - j) <= k`.
**Phân tích thuật toán:** Sử dụng Fixed-size Window kết hợp HashSet. Duy trì một HashSet chứa nhiều nhất `k` phần tử gần nhất. Khi duyệt `right` từ đầu đến cuối mảng, nếu `nums[right]` đã có trong Set, trả về `true`. Nếu Set chứa nhiều hơn `k` phần tử, xóa phần tử bị trượt ra khỏi cửa sổ (`nums[i - k]`).
**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class ContainsDuplicateII {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        Set<Integer> window = new HashSet<>();
        
        for (int i = 0; i < nums.length; i++) {
            if (window.contains(nums[i])) {
                return true;
            }
            window.add(nums[i]);
            
            if (window.size() > k) {
                window.remove(nums[i - k]);
            }
        }
        return false;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(\min(N, K))$.

---

## 16. Contains Duplicate III (LeetCode 220)
**Đề bài chi tiết:** Cho mảng số nguyên `nums`, hai số nguyên `indexDiff` và `valueDiff`. Tìm cặp `(i, j)` sao cho `abs(i - j) <= indexDiff` và `abs(nums[i] - nums[j]) <= valueDiff`.
**Phân tích thuật toán:** Kết hợp Sliding Window và TreeSet. Dùng TreeSet để lưu cửa sổ tối đa `indexDiff` phần tử. Với mỗi `nums[i]`, kiểm tra xem trong TreeSet có phần tử nào nằm trong đoạn `[nums[i] - valueDiff, nums[i] + valueDiff]` hay không (dùng `ceiling`).
**Mã nguồn Java:**
```java
import java.util.TreeSet;

public class ContainsDuplicateIII {
    public boolean containsNearbyAlmostDuplicate(int[] nums, int indexDiff, int valueDiff) {
        TreeSet<Long> set = new TreeSet<>();
        
        for (int i = 0; i < nums.length; i++) {
            long num = (long) nums[i];
            
            // Tìm số nhỏ nhất lớn hơn hoặc bằng (num - valueDiff)
            Long floor = set.ceiling(num - valueDiff);
            if (floor != null && floor <= num + valueDiff) {
                return true;
            }
            
            set.add(num);
            if (set.size() > indexDiff) {
                set.remove((long) nums[i - indexDiff]);
            }
        }
        
        return false;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log K)$, Space $\mathcal{O}(K)$ (với $K$ là `indexDiff`).

---

## 17. Number of Sub-arrays of Size K and Average Greater than or Equal to Threshold (LeetCode 1343)
**Đề bài chi tiết:** Cho một mảng `nums` và hai số `k`, `threshold`. Tìm số lượng mảng con liên tiếp có kích thước bằng `k` và có trung bình cộng $\ge threshold$.
**Phân tích thuật toán:** Bài toán Fixed-size Window kinh điển. Để tránh chia cho số thực, ta quy đổi điều kiện trung bình cộng thành: Tính `targetSum = k * threshold`. Trượt cửa sổ kích thước `k`, tính tổng các phần tử. Nếu tổng $\ge targetSum$, ta đếm thêm 1 mảng con hợp lệ.
**Mã nguồn Java:**
```java
public class NumSubarraysSizeK {
    public int numOfSubarrays(int[] nums, int k, int threshold) {
        int targetSum = k * threshold;
        int count = 0;
        int currentSum = 0;
        
        for (int i = 0; i < nums.length; i++) {
            currentSum += nums[i];
            
            if (i >= k - 1) {
                if (currentSum >= targetSum) {
                    count++;
                }
                currentSum -= nums[i - k + 1];
            }
        }
        
        return count;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 18. Maximum Erasure Value (LeetCode 1695)
**Đề bài chi tiết:** Cho mảng `nums`. Tìm một mảng con liên tiếp chỉ chứa các phần tử **không trùng lặp**, sao cho **tổng** của các phần tử trong mảng con đó là lớn nhất.
**Phân tích thuật toán:** Tương tự bài Longest Substring Without Repeating Characters. Sử dụng Variable-size Window và HashSet (hoặc mảng đếm). Trượt `right`, cộng `nums[right]` vào `currentSum`. Nếu gặp số trùng, đẩy `left` lên và trừ các số đã ra khỏi cửa sổ cho tới khi mảng con hoàn toàn không có phần tử lặp. Cập nhật `maxSum`.
**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class MaximumErasureValue {
    public int maximumUniqueSubarray(int[] nums) {
        Set<Integer> set = new HashSet<>();
        int left = 0, currentSum = 0, maxSum = 0;
        
        for (int right = 0; right < nums.length; right++) {
            while (set.contains(nums[right])) {
                set.remove(nums[left]);
                currentSum -= nums[left];
                left++;
            }
            
            set.add(nums[right]);
            currentSum += nums[right];
            maxSum = Math.max(maxSum, currentSum);
        }
        
        return maxSum;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 19. Get Equal Substrings Within Budget (LeetCode 1208)
**Đề bài chi tiết:** Cho hai chuỗi `s` và `t` cùng độ dài. Chi phí để đổi ký tự `s[i]` thành `t[i]` là `abs(s[i] - t[i])`. Bạn có ngân sách `maxCost`. Tìm chuỗi con dài nhất có thể thay đổi sao cho tổng chi phí $\le maxCost$.
**Phân tích thuật toán:** Variable-size Window. Duy trì biến `currentCost` tổng chi phí trong cửa sổ hiện tại. Khi cộng dồn chi phí biến đổi ở `right` mà vượt quá `maxCost`, ta thu hẹp bằng cách đẩy `left` lên và trừ đi chi phí tương ứng ở bên trái cho tới khi thoả mãn điều kiện ngân sách. Trả về độ dài cửa sổ max.
**Mã nguồn Java:**
```java
public class EqualSubstringBudget {
    public int equalSubstring(String s, String t, int maxCost) {
        int left = 0;
        int currentCost = 0;
        int maxLen = 0;
        
        for (int right = 0; right < s.length(); right++) {
            currentCost += Math.abs(s.charAt(right) - t.charAt(right));
            
            while (currentCost > maxCost) {
                currentCost -= Math.abs(s.charAt(left) - t.charAt(left));
                left++;
            }
            
            maxLen = Math.max(maxLen, right - left + 1);
        }
        
        return maxLen;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 20. Count Number of Nice Subarrays (LeetCode 1248)
**Đề bài chi tiết:** Một mảng con được coi là "Nice" nếu nó có chứa chính xác `k` số lẻ. Trả về số lượng các "Nice Subarrays" có trong mảng.
**Phân tích thuật toán:** Tương tự bài toán K Different Integers: số mảng chứa chính xác `k` số lẻ có thể được tính bằng công thức `atMost(K) - atMost(K-1)`. Hàm `atMost(x)` đếm số lượng mảng con có tối đa `x` số lẻ bằng Variable-size Window.
**Mã nguồn Java:**
```java
public class NiceSubarrays {
    public int numberOfSubarrays(int[] nums, int k) {
        return atMost(nums, k) - atMost(nums, k - 1);
    }
    
    private int atMost(int[] nums, int k) {
        int left = 0, res = 0, oddCount = 0;
        
        for (int right = 0; right < nums.length; right++) {
            if (nums[right] % 2 != 0) {
                oddCount++;
            }
            
            while (oddCount > k) {
                if (nums[left] % 2 != 0) {
                    oddCount--;
                }
                left++;
            }
            // Số lượng mảng con hợp lệ kết thúc tại right
            res += right - left + 1;
        }
        
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 21. Maximum Points You Can Obtain from Cards (LeetCode 1423)
**Đề bài chi tiết:** Cho mảng số nguyên `cardPoints` và số nguyên `k`. Trong mỗi bước, bạn có thể lấy một thẻ từ đầu hoặc cuối mảng. Hãy tìm tổng điểm tối đa có thể đạt được sau đúng `k` bước.
**Phân tích thuật toán:** Thay vì tính tổng các phần tử bị lấy ở hai đầu (khá phức tạp), ta có thể lấy tổng toàn bộ mảng trừ đi một mảng con liên tiếp ở giữa có kích thước `n - k`. Bài toán trở thành tìm Fixed-size Window kích thước `n - k` có tổng NHỎ NHẤT. Tổng lớn nhất cần tìm sẽ là: `Tổng toàn mảng - Tổng nhỏ nhất của Window`.
**Mã nguồn Java:**
```java
public class MaxScoreCards {
    public int maxScore(int[] cardPoints, int k) {
        int n = cardPoints.length;
        int totalSum = 0;
        for (int point : cardPoints) totalSum += point;
        
        if (k == n) return totalSum;
        
        int windowSize = n - k;
        int currentSum = 0;
        int minWindowSum = Integer.MAX_VALUE;
        
        for (int i = 0; i < n; i++) {
            currentSum += cardPoints[i];
            
            if (i >= windowSize - 1) {
                minWindowSum = Math.min(minWindowSum, currentSum);
                currentSum -= cardPoints[i - windowSize + 1];
            }
        }
        
        return totalSum - minWindowSum;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 22. Replace the Substring for Balanced String (LeetCode 1234)
**Đề bài chi tiết:** Một chuỗi độ dài `n` chỉ chứa 'Q', 'W', 'E', 'R' được gọi là cân bằng nếu mỗi ký tự xuất hiện đúng `n/4` lần. Tìm chiều dài ngắn nhất của mảng con (chuỗi con liên tiếp) cần được thay thế bằng bất kỳ chuỗi nào khác để chuỗi ban đầu trở thành cân bằng.
**Phân tích thuật toán:** Bài toán yêu cầu phần còn lại CỦA CHUỖI (bên ngoài mảng con bị thay thế) không có ký tự nào vượt quá `n/4` lần. Ta dùng Variable-size Window. Đếm toàn bộ tần suất ban đầu. Sau đó, trượt Window: mỗi lần giảm tần suất ký tự tại `right` (giả sử nó bị thay thế). Khi tất cả ký tự bên ngoài Window đều $\le n/4$, ta ghi nhận độ dài Window và cố gắng thu hẹp `left` để tìm độ dài ngắn hơn.
**Mã nguồn Java:**
```java
public class ReplaceSubstringBalanced {
    public int balancedString(String s) {
        int[] count = new int[128];
        int n = s.length();
        int k = n / 4;
        
        for (int i = 0; i < n; i++) {
            count[s.charAt(i)]++;
        }
        
        int left = 0, minLen = n;
        for (int right = 0; right < n; right++) {
            count[s.charAt(right)]--;
            
            while (left < n && count['Q'] <= k && count['W'] <= k && count['E'] <= k && count['R'] <= k) {
                minLen = Math.min(minLen, right - left + 1);
                count[s.charAt(left)]++;
                left++;
            }
        }
        
        return minLen;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 23. Sliding Window Median (LeetCode 480)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` và số `k`. Trả về mảng chứa trung vị (median) của mọi cửa sổ trượt kích thước `k`.
**Phân tích thuật toán:** Sử dụng hai Priority Queue: `MaxHeap` lưu nửa nhỏ hơn và `MinHeap` lưu nửa lớn hơn của Window. Duy trì sự cân bằng: kích thước `MaxHeap` luôn bằng hoặc lớn hơn 1 phần tử so với `MinHeap`. Trượt Window, thêm phần tử mới và xoá phần tử cũ (sử dụng phương thức `.remove(Object)`). Do `.remove()` trong PQ mất $\mathcal{O}(K)$, thuật toán có thời gian $\mathcal{O}(N \cdot K)$, đủ để pass phỏng vấn ở mức cơ bản (có thể tối ưu bằng Tree Map hoặc Lazy Deletion).
**Mã nguồn Java:**
```java
import java.util.Comparator;
import java.util.PriorityQueue;

public class SlidingWindowMedian {
    public double[] medianSlidingWindow(int[] nums, int k) {
        double[] res = new double[nums.length - k + 1];
        PriorityQueue<Integer> left = new PriorityQueue<>(Comparator.reverseOrder()); // Max-Heap
        PriorityQueue<Integer> right = new PriorityQueue<>(); // Min-Heap
        
        for (int i = 0; i < nums.length; i++) {
            // Thêm phần tử
            if (left.size() <= right.size()) {
                right.add(nums[i]);
                left.add(right.poll());
            } else {
                left.add(nums[i]);
                right.add(left.poll());
            }
            
            // Xử lý khi cửa sổ đạt kích thước K
            if (i >= k - 1) {
                if (k % 2 == 0) {
                    res[i - k + 1] = ((double) left.peek() + (double) right.peek()) / 2.0;
                } else {
                    res[i - k + 1] = (double) left.peek();
                }
                
                // Loại bỏ phần tử trượt ra khỏi cửa sổ
                int removeElement = nums[i - k + 1];
                if (removeElement <= left.peek()) {
                    left.remove(removeElement);
                } else {
                    right.remove(removeElement);
                }
                
                // Cân bằng lại 2 Heap
                if (left.size() > right.size() + 1) {
                    right.add(left.poll());
                } else if (left.size() < right.size()) {
                    left.add(right.poll());
                }
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \cdot K)$ (Do hàm `remove` của PriorityQueue tốn $\mathcal{O}(K)$), Space $\mathcal{O}(K)$.

---

## 24. Constrained Subsequence Sum (LeetCode 1425)
**Đề bài chi tiết:** Cho mảng `nums` và số `k`. Tìm tổng lớn nhất của một mảng con không nhất thiết liên tiếp (subsequence) sao cho khoảng cách giữa hai chỉ số của phần tử liên tiếp trong subsequence không vượt quá `k` ($i - j \le k$).
**Phân tích thuật toán:** Áp dụng Quy hoạch động (DP): `dp[i]` là tổng lớn nhất kết thúc tại `i`. Chuyển trạng thái: `dp[i] = nums[i] + max(0, dp[j])` với `i - k <= j < i`. Thay vì duyệt lại `k` phần tử trước đó (mất $\mathcal{O}(N \cdot K)$), ta dùng Monotonic Deque (Sliding Window Max) để lấy Max `dp[j]` trong cửa sổ kích thước `k` tối ưu xuống thời gian tuyến tính.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class ConstrainedSubsequenceSum {
    public int constrainedSubsetSum(int[] nums, int k) {
        int n = nums.length;
        int[] dp = new int[n];
        Deque<Integer> deque = new ArrayDeque<>();
        int maxSum = Integer.MIN_VALUE;
        
        for (int i = 0; i < n; i++) {
            // Loại bỏ chỉ số ngoài cửa sổ k
            if (!deque.isEmpty() && i - deque.peekFirst() > k) {
                deque.pollFirst();
            }
            
            dp[i] = nums[i];
            if (!deque.isEmpty() && dp[deque.peekFirst()] > 0) {
                dp[i] += dp[deque.peekFirst()];
            }
            
            maxSum = Math.max(maxSum, dp[i]);
            
            // Duy trì Deque giảm dần
            while (!deque.isEmpty() && dp[deque.peekLast()] <= dp[i]) {
                deque.pollLast();
            }
            
            deque.offerLast(i);
        }
        
        return maxSum;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$ (có thể tối ưu Space xuống $\mathcal{O}(K)$ nếu chỉ lưu `k` biến `dp`).

---

## 25. Maximum Number of Vowels in a Substring of Given Length (LeetCode 1456)
**Đề bài chi tiết:** Cho chuỗi `s` và số `k`. Tìm số lượng ký tự nguyên âm tối đa trong bất kỳ mảng con liên tiếp nào có độ dài bằng `k`. Nguyên âm gồm 'a', 'e', 'i', 'o', 'u'.
**Phân tích thuật toán:** Bài toán Fixed-size Window kinh điển. Trượt cửa sổ kích thước `k`, tính số nguyên âm hiện tại (`currentVowels`). Nếu có kí tự nguyên âm trượt vào thì cộng thêm, nếu kí tự nguyên âm trượt ra thì trừ đi. Cập nhật số lượng Max.
**Mã nguồn Java:**
```java
public class MaxVowels {
    public int maxVowels(String s, int k) {
        int maxVowels = 0;
        int currentVowels = 0;
        
        for (int i = 0; i < s.length(); i++) {
            if (isVowel(s.charAt(i))) {
                currentVowels++;
            }
            
            if (i >= k) {
                if (isVowel(s.charAt(i - k))) {
                    currentVowels--;
                }
            }
            
            maxVowels = Math.max(maxVowels, currentVowels);
        }
        
        return maxVowels;
    }
    
    private boolean isVowel(char c) {
        return c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u';
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 26. Find the K-Beauty of a Number (LeetCode 2269)
**Đề bài chi tiết:** K-beauty của số nguyên `num` được định nghĩa là số lượng các chuỗi con liền kề có độ dài `k` trong dạng chuỗi của `num` mà chia hết cho `num` (và không bằng 0). Tìm K-beauty của `num`.
**Phân tích thuật toán:** Chuyển số `num` thành chuỗi. Trượt cửa sổ độ dài `k` (sử dụng `substring`). Ép kiểu ngược lại về số nguyên và kiểm tra điều kiện chia hết: `val != 0 && num % val == 0`.
**Mã nguồn Java:**
```java
public class KBeauty {
    public int divisorSubstrings(int num, int k) {
        String s = String.valueOf(num);
        int count = 0;
        
        for (int i = 0; i <= s.length() - k; i++) {
            String sub = s.substring(i, i + k);
            int val = Integer.parseInt(sub);
            
            if (val != 0 && num % val == 0) {
                count++;
            }
        }
        
        return count;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \cdot K)$ (với N là số chữ số, hàm `substring` lấy chuỗi độ dài K), Space $\mathcal{O}(N)$. Do $N \le 10$, $\mathcal{O}(1)$ trên thực tế.

---

## 27. Continuous Subarrays (LeetCode 2762)
**Đề bài chi tiết:** Trả về tổng số mảng con liên tiếp (continuous subarrays) sao cho độ chênh lệch tuyệt đối giữa bất kỳ hai phần tử nào trong mảng con đó không vượt quá 2.
**Phân tích thuật toán:** Tương đương việc phần tử Max và phần tử Min trong cửa sổ có độ chênh lệch $\le 2$. Sử dụng `TreeMap` để đếm số lượng và duy trì các phần tử đã sắp xếp. Trượt `right`, đẩy vào TreeMap. Nếu `map.lastKey() - map.firstKey() > 2`, cửa sổ vi phạm, ta trượt `left` để loại bỏ dần phần tử cũ ra khỏi TreeMap cho tới khi hợp lệ. Số mảng con hợp lệ chính là `right - left + 1`.
**Mã nguồn Java:**
```java
import java.util.TreeMap;

public class ContinuousSubarrays {
    public long continuousSubarrays(int[] nums) {
        TreeMap<Integer, Integer> map = new TreeMap<>();
        int left = 0;
        long count = 0;
        
        for (int right = 0; right < nums.length; right++) {
            map.put(nums[right], map.getOrDefault(nums[right], 0) + 1);
            
            while (map.lastKey() - map.firstKey() > 2) {
                int leftNum = nums[left];
                map.put(leftNum, map.get(leftNum) - 1);
                if (map.get(leftNum) == 0) {
                    map.remove(leftNum);
                }
                left++;
            }
            
            count += right - left + 1;
        }
        
        return count;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log K)$ (K tối đa là 3 vì các số chênh lệch tối đa là 2, nên Time $\mathcal{O}(N)$), Space $\mathcal{O}(1)$ (TreeMap size tối đa 3).

---

## 28. Minimum Operations to Reduce X to Zero (LeetCode 1658)
**Đề bài chi tiết:** Cho mảng `nums` và số `x`. Mỗi bước có thể loại bỏ phần tử ở đầu hoặc cuối mảng và trừ `x` đi giá trị đó. Tìm số bước tối thiểu để `x` giảm đúng bằng 0.
**Phân tích thuật toán:** Bài toán nghịch đảo: Thay vì tìm các phần tử ở 2 đầu có tổng bằng `x`, ta tìm Mảng con dài nhất ở giữa có tổng bằng `Sum - x` (`target`). Áp dụng Variable-size Window tìm Longest Subarray có tổng bằng `target`. Nếu tìm được mảng dài nhất `maxLen`, kết quả là `n - maxLen`.
**Mã nguồn Java:**
```java
public class MinOperationsReduceX {
    public int minOperations(int[] nums, int x) {
        int totalSum = 0;
        for (int num : nums) totalSum += num;
        
        int target = totalSum - x;
        if (target < 0) return -1; // Không thể đạt được
        if (target == 0) return nums.length;
        
        int left = 0, currentSum = 0;
        int maxLen = -1;
        
        for (int right = 0; right < nums.length; right++) {
            currentSum += nums[right];
            
            while (currentSum > target && left <= right) {
                currentSum -= nums[left];
                left++;
            }
            
            if (currentSum == target) {
                maxLen = Math.max(maxLen, right - left + 1);
            }
        }
        
        return maxLen == -1 ? -1 : nums.length - maxLen;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 29. Max Consecutive Ones II (LeetCode 487)
**Đề bài chi tiết:** Cho mảng nhị phân, tìm số lượng số 1 liên tiếp lớn nhất nếu bạn có thể lật ĐÚNG 1 số 0 thành số 1.
**Phân tích thuật toán:** Tương tự Max Consecutive Ones III nhưng ở đây `k = 1`. Dùng Variable-size Window, đếm số lượng số 0 trong cửa sổ (`zeroCount`). Khi `zeroCount > 1`, đẩy `left` lên cho đến khi loại bỏ được một số 0. Cập nhật độ dài lớn nhất.
**Mã nguồn Java:**
```java
public class MaxConsecutiveOnesII {
    public int findMaxConsecutiveOnes(int[] nums) {
        int left = 0;
        int zeroCount = 0;
        int maxLen = 0;
        
        for (int right = 0; right < nums.length; right++) {
            if (nums[right] == 0) {
                zeroCount++;
            }
            
            while (zeroCount > 1) {
                if (nums[left] == 0) {
                    zeroCount--;
                }
                left++;
            }
            
            maxLen = Math.max(maxLen, right - left + 1);
        }
        
        return maxLen;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 30. Frequency of the Most Frequent Element (LeetCode 1838)
**Đề bài chi tiết:** Bạn có thể thực hiện tối đa `k` thao tác, mỗi thao tác cộng thêm 1 vào một phần tử tùy ý. Tìm tần suất xuất hiện cao nhất của một phần tử sau thao tác.
**Phân tích thuật toán:** Sắp xếp mảng. Trượt Window: để mọi phần tử trong Window bằng với `nums[right]` (phần tử lớn nhất trong Window), tổng số thao tác cần là `nums[right] * windowSize - windowSum`. Nếu số lượng thao tác này lớn hơn `k`, Window không hợp lệ $\rightarrow$ ta phải thu hẹp bằng cách đẩy `left` lên và trừ giá trị khỏi `windowSum`.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class FrequencyMostFrequentElement {
    public int maxFrequency(int[] nums, int k) {
        Arrays.sort(nums); // Bước quan trọng để cộng dồn lên
        int left = 0;
        long windowSum = 0;
        int maxFreq = 0;
        
        for (int right = 0; right < nums.length; right++) {
            windowSum += nums[right];
            
            long expectedSum = (long) nums[right] * (right - left + 1);
            
            while (expectedSum - windowSum > k) {
                windowSum -= nums[left];
                left++;
                expectedSum = (long) nums[right] * (right - left + 1);
            }
            
            maxFreq = Math.max(maxFreq, right - left + 1);
        }
        
        return maxFreq;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log N)$ (do sắp xếp), Space $\mathcal{O}(1)$.
