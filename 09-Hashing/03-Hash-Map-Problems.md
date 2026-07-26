# Hash Map Problems

Danh sách 30 bài tập ứng dụng Hash Map từ cơ bản đến nâng cao. 10 bài đầu tiên sẽ có đầy đủ đề bài, phân tích thuật toán, mã nguồn Java và phân tích độ phức tạp. Các bài sau được tóm tắt.

## 1. Group Anagrams (Gom nhóm từ đảo chữ)

**Đề bài chi tiết:**
Cho một mảng các chuỗi `strs`, hãy gom nhóm các anagram (các từ cấu tạo từ cùng các ký tự giống nhau nhưng có thể khác vị trí) lại với nhau. Bạn có thể trả về đáp án theo bất kỳ thứ tự nào.
Ví dụ: `strs = ["eat","tea","tan","ate","nat","bat"]`
Đầu ra: `[["bat"],["nat","tan"],["ate","eat","tea"]]`

**Phân tích thuật toán:**
Hai chuỗi là anagram của nhau nếu khi sắp xếp các ký tự của chúng theo thứ tự chữ cái, ta thu được cùng một chuỗi. 
Ta sử dụng Hash Map, trong đó:
- Key: Chuỗi sau khi đã được sắp xếp (Signature).
- Value: Danh sách các chuỗi gốc có cùng Signature này.
Duyệt qua từng chuỗi, sắp xếp ký tự, dùng làm Key trong Hash Map, và thêm chuỗi gốc vào Value List tương ứng.

**Mã nguồn Java:**
```java
import java.util.*;

public class GroupAnagrams {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        
        for (String s : strs) {
            char[] chars = s.toCharArray();
            Arrays.sort(chars);
            String sortedKey = new String(chars);
            
            map.computeIfAbsent(sortedKey, k -> new ArrayList<>()).add(s);
        }
        
        return new ArrayList<>(map.values());
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N * K log K)` với N là số lượng chuỗi, K là độ dài tối đa của một chuỗi (do dùng sắp xếp `Arrays.sort`).
- **Space Complexity:** `O(N * K)` để lưu trữ toàn bộ dữ liệu trong Hash Map.

---

## 2. Top K Frequent Elements (K phần tử xuất hiện nhiều nhất)

**Đề bài chi tiết:**
Cho một mảng số nguyên `nums` và một số nguyên `k`, trả về `k` phần tử xuất hiện nhiều nhất trong mảng.
Ví dụ: `nums = [1,1,1,2,2,3], k = 2`
Đầu ra: `[1,2]`

**Phân tích thuật toán:**
Bài này chia làm hai bước:
1. Đếm tần suất xuất hiện của mỗi số bằng Hash Map (`Map<Integer, Integer>`).
2. Tìm `k` phần tử có tần suất cao nhất. Có thể dùng PriorityQueue (Min-Heap) dựa trên tần suất để giữ đúng `k` phần tử, hoặc dùng Bucket Sort. Dưới đây dùng Min-Heap.

**Mã nguồn Java:**
```java
import java.util.*;

public class TopKFrequentElements {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> countMap = new HashMap<>();
        for (int num : nums) {
            countMap.put(num, countMap.getOrDefault(num, 0) + 1);
        }
        
        // Min Heap sắp xếp theo tần suất xuất hiện (Value của Map)
        PriorityQueue<Integer> heap = new PriorityQueue<>(
            (n1, n2) -> countMap.get(n1) - countMap.get(n2)
        );
        
        for (int num : countMap.keySet()) {
            heap.add(num);
            if (heap.size() > k) {
                heap.poll(); // Bỏ phần tử có tần suất nhỏ nhất
            }
        }
        
        int[] result = new int[k];
        for (int i = 0; i < k; i++) {
            result[i] = heap.poll();
        }
        return result;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N log k)` với N là số lượng phần tử. Đếm tần suất mất `O(N)`, duy trì Heap kích thước k mất `O(N log k)`.
- **Space Complexity:** `O(N)` để lưu Map tần suất.

---

## 3. Isomorphic Strings (Hai chuỗi đồng hình)

**Đề bài chi tiết:**
Cho hai chuỗi `s` và `t`, kiểm tra xem chúng có phải là đồng hình (isomorphic) hay không. Hai chuỗi là đồng hình nếu các ký tự trong `s` có thể được thay thế để tạo thành `t`. Mọi ký tự giống nhau phải map tới cùng một ký tự khác, và không có hai ký tự khác nhau nào được map tới cùng một ký tự.
Ví dụ: `s = "egg", t = "add"` -> Đầu ra: `true`.

**Phân tích thuật toán:**
Dùng 2 Hash Map hoặc 1 Hash Map kết hợp với việc kiểm tra Map chứa value chưa. 
Ánh xạ `map(s[i]) -> t[i]`.
Nếu ký tự `s[i]` đã có trong Map, kiểm tra xem nó có map đúng tới `t[i]` không.
Đồng thời, đảm bảo không có 2 ký tự của `s` cùng map tới một ký tự của `t`.

**Mã nguồn Java:**
```java
import java.util.*;

public class IsomorphicStrings {
    public boolean isIsomorphic(String s, String t) {
        if (s.length() != t.length()) return false;
        
        Map<Character, Character> mapST = new HashMap<>();
        Map<Character, Character> mapTS = new HashMap<>();
        
        for (int i = 0; i < s.length(); i++) {
            char c1 = s.charAt(i);
            char c2 = t.charAt(i);
            
            if (mapST.containsKey(c1) && mapST.get(c1) != c2) {
                return false;
            }
            if (mapTS.containsKey(c2) && mapTS.get(c2) != c1) {
                return false;
            }
            
            mapST.put(c1, c2);
            mapTS.put(c2, c1);
        }
        
        return true;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N)` với N là chiều dài chuỗi.
- **Space Complexity:** `O(1)` (Do tập ký tự giới hạn, tối đa 256 ký tự ASCII).

---

## 4. Word Pattern (Mẫu từ)

**Đề bài chi tiết:**
Cho một `pattern` (mẫu) và một chuỗi `s`, xác định xem `s` có theo đúng `pattern` hay không. Tương tự Isomorphic Strings nhưng ánh xạ giữa ký tự và các từ cách nhau bởi khoảng trắng.
Ví dụ: `pattern = "abba", s = "dog cat cat dog"` -> `true`.

**Phân tích thuật toán:**
Cắt chuỗi `s` thành mảng các từ `words`.
Dùng Hash Map ánh xạ từ ký tự `pattern[i]` sang `words[i]`. Dùng HashSet để đảm bảo hai ký tự pattern khác nhau không ánh xạ vào cùng một từ.

**Mã nguồn Java:**
```java
import java.util.*;

public class WordPattern {
    public boolean wordPattern(String pattern, String s) {
        String[] words = s.split(" ");
        if (words.length != pattern.length()) return false;
        
        Map<Character, String> charToWord = new HashMap<>();
        Set<String> wordSeen = new HashSet<>();
        
        for (int i = 0; i < pattern.length(); i++) {
            char c = pattern.charAt(i);
            String word = words[i];
            
            if (charToWord.containsKey(c)) {
                if (!charToWord.get(c).equals(word)) return false;
            } else {
                if (wordSeen.contains(word)) return false;
                charToWord.put(c, word);
                wordSeen.add(word);
            }
        }
        
        return true;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N + M)` với N là độ dài pattern, M là độ dài string (để cắt thành từ).
- **Space Complexity:** `O(W)` với W là số lượng từ độc nhất.

---

## 5. Sort Characters By Frequency (Sắp xếp ký tự theo tần suất)

**Đề bài chi tiết:**
Cho một chuỗi `s`, sắp xếp chuỗi theo thứ tự giảm dần dựa trên tần suất xuất hiện của các ký tự. 
Ví dụ: `s = "tree"` -> `"eert"` hoặc `"eetr"`.

**Phân tích thuật toán:**
1. Đếm tần suất mỗi ký tự dùng Hash Map.
2. Lưu các ký tự vào danh sách và sắp xếp danh sách giảm dần theo Value trong Hash Map (Hoặc dùng Heap/Bucket Sort).
3. Xây dựng lại chuỗi với `StringBuilder`.

**Mã nguồn Java:**
```java
import java.util.*;

public class SortCharactersByFrequency {
    public String frequencySort(String s) {
        Map<Character, Integer> map = new HashMap<>();
        for (char c : s.toCharArray()) {
            map.put(c, map.getOrDefault(c, 0) + 1);
        }
        
        List<Character> list = new ArrayList<>(map.keySet());
        list.sort((a, b) -> map.get(b) - map.get(a));
        
        StringBuilder sb = new StringBuilder();
        for (char c : list) {
            int count = map.get(c);
            for (int i = 0; i < count; i++) {
                sb.append(c);
            }
        }
        return sb.toString();
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N + K log K)` với N là chiều dài chuỗi, K là số lượng ký tự độc nhất (Tối đa 256, nên coi như `O(N)`).
- **Space Complexity:** `O(N)` lưu trữ StringBuilder kết quả và tần suất.

---

## 6. Find All Anagrams in a String (Tìm mọi Anagram trong chuỗi)

**Đề bài chi tiết:**
Cho hai chuỗi `s` và `p`, trả về mảng các vị trí bắt đầu (index) của tất cả các chuỗi con trong `s` là anagram của `p`.
Ví dụ: `s = "cbaebabacd", p = "abc"` -> `[0, 6]`

**Phân tích thuật toán:**
Dùng phương pháp Sliding Window + Hashing (thường dùng Mảng kích thước 26 vì chỉ có chữ thường, nhưng tư duy là Hash Map đếm ký tự). 
Lưu tần suất ký tự của `p`. Quét window kích thước bằng `p.length()` qua `s`. Mỗi khi window dịch sang phải, thêm ký tự mới vào Map, bớt ký tự cũ ra khỏi Map, nếu Map cửa sổ giống Map của `p` thì là kết quả hợp lệ.

**Mã nguồn Java:**
```java
import java.util.*;

public class FindAllAnagrams {
    public List<Integer> findAnagrams(String s, String p) {
        List<Integer> result = new ArrayList<>();
        if (s.length() < p.length()) return result;
        
        int[] pCount = new int[26];
        int[] sCount = new int[26];
        
        for (char c : p.toCharArray()) {
            pCount[c - 'a']++;
        }
        
        for (int i = 0; i < s.length(); i++) {
            sCount[s.charAt(i) - 'a']++;
            
            // Xóa ký tự thoát khỏi cửa sổ
            if (i >= p.length()) {
                sCount[s.charAt(i - p.length()) - 'a']--;
            }
            
            // So sánh 2 mảng tần suất
            if (Arrays.equals(pCount, sCount)) {
                result.add(i - p.length() + 1);
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N)` với N là chiều dài chuỗi `s`. So sánh mảng độ dài 26 mất thời gian hằng số `O(1)`.
- **Space Complexity:** `O(1)` (hai mảng kích thước cố định 26).

---

## 7. Ransom Note (Thư tống tiền)

**Đề bài chi tiết:**
Cho hai chuỗi `ransomNote` và `magazine`, trả về `true` nếu bạn có thể tạo thành `ransomNote` bằng cách cắt các chữ cái từ `magazine` và `false` nếu không thể. Mỗi chữ cái từ `magazine` chỉ được dùng 1 lần.

**Phân tích thuật toán:**
Dùng Hash Map (hoặc mảng đếm tần suất) đếm số lượng các ký tự có trong `magazine`. Sau đó duyệt qua từng ký tự của `ransomNote`, nếu ký tự đó tồn tại và số lượng > 0 trong map thì trừ đi 1, nếu hết hoặc không có thì trả về false.

**Mã nguồn Java:**
```java
import java.util.*;

public class RansomNote {
    public boolean canConstruct(String ransomNote, String magazine) {
        Map<Character, Integer> counts = new HashMap<>();
        for (char c : magazine.toCharArray()) {
            counts.put(c, counts.getOrDefault(c, 0) + 1);
        }
        
        for (char c : ransomNote.toCharArray()) {
            if (!counts.containsKey(c) || counts.get(c) == 0) {
                return false;
            }
            counts.put(c, counts.get(c) - 1);
        }
        
        return true;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(M + N)` với M, N là chiều dài hai chuỗi.
- **Space Complexity:** `O(1)` (tối đa 26 ký tự chữ cái).

---

## 8. Continuous Subarray Sum (Tổng mảng con liên tiếp chia hết cho K)

**Đề bài chi tiết:**
Cho mảng số nguyên `nums` và số `k`, kiểm tra xem có mảng con liên tiếp nào có độ dài ít nhất là 2 mà tổng các phần tử của nó chia hết cho `k` không.

**Phân tích thuật toán:**
Dùng Prefix Sum + Hash Map. 
Nếu prefix sum tại `j` và prefix sum tại `i` (với `i < j`) có cùng số dư khi chia cho `k`, thì tổng các phần tử từ `i+1` đến `j` chia hết cho `k`.
Hash Map lưu trữ: Key là (PrefixSum % k), Value là Index (vị trí đầu tiên tìm thấy số dư này).

**Mã nguồn Java:**
```java
import java.util.*;

public class ContinuousSubarraySum {
    public boolean checkSubarraySum(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, -1); // Xử lý trường hợp mảng con bắt đầu từ index 0
        int prefixSum = 0;
        
        for (int i = 0; i < nums.length; i++) {
            prefixSum += nums[i];
            int remainder = k == 0 ? prefixSum : prefixSum % k;
            
            if (map.containsKey(remainder)) {
                if (i - map.get(remainder) > 1) { // Độ dài ít nhất 2
                    return true;
                }
            } else {
                map.put(remainder, i); // Chỉ ghi lần đầu tiên xuất hiện để tối đa độ dài
            }
        }
        return false;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N)` duyệt qua mảng 1 lần.
- **Space Complexity:** `O(min(N, K))` lưu trữ số dư trong map.

---

## 9. Subarray Sum Equals K (Số lượng mảng con có tổng bằng K)

**Đề bài chi tiết:**
Cho một mảng số nguyên `nums` và số `k`. Trả về số lượng mảng con liên tiếp có tổng đúng bằng `k`.

**Phân tích thuật toán:**
Dùng Prefix Sum kết hợp Hash Map. Tại mỗi bước `i`, tính tổng `prefixSum`. Ta cần tìm xem trước đó có đoạn nào có tổng bằng `prefixSum - k` hay không. 
Hash Map sẽ lưu: Key = Prefix Sum, Value = Số lần xuất hiện của Prefix Sum này.

**Mã nguồn Java:**
```java
import java.util.*;

public class SubarraySumEqualsK {
    public int subarraySum(int[] nums, int k) {
        int count = 0, prefixSum = 0;
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, 1); // Tổng 0 xuất hiện 1 lần lúc ban đầu
        
        for (int num : nums) {
            prefixSum += num;
            if (map.containsKey(prefixSum - k)) {
                count += map.get(prefixSum - k);
            }
            map.put(prefixSum, map.getOrDefault(prefixSum, 0) + 1);
        }
        
        return count;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N)`
- **Space Complexity:** `O(N)` cho Hash Map.

---

## 10. Minimum Window Substring (Cửa sổ chuỗi con nhỏ nhất)

**Đề bài chi tiết:**
Cho hai chuỗi `s` và `t`. Tìm chuỗi con ngắn nhất trong `s` sao cho nó chứa tất cả các ký tự của `t` (bao gồm cả số lượng ký tự lặp lại). Nếu không có, trả về chuỗi rỗng `""`.

**Phân tích thuật toán:**
Kết hợp Sliding Window và Hash Map.
Dùng Hash Map lưu số lượng ký tự cần thiết của `t`. Dùng 2 con trỏ `left` và `right` tạo cửa sổ. Mở rộng `right` để gom đủ ký tự. Khi đủ, thu hẹp `left` liên tục để tối ưu chiều dài nhỏ nhất, cho đến khi không còn đủ ký tự.

**Mã nguồn Java:**
```java
import java.util.*;

public class MinimumWindowSubstring {
    public String minWindow(String s, String t) {
        if (s.length() == 0 || t.length() == 0) return "";
        
        Map<Character, Integer> dictT = new HashMap<>();
        for (char c : t.toCharArray()) dictT.put(c, dictT.getOrDefault(c, 0) + 1);
        
        int required = dictT.size();
        int left = 0, right = 0;
        int formed = 0;
        
        Map<Character, Integer> windowCounts = new HashMap<>();
        int[] ans = {-1, 0, 0}; // length, left, right
        
        while (right < s.length()) {
            char c = s.charAt(right);
            windowCounts.put(c, windowCounts.getOrDefault(c, 0) + 1);
            
            if (dictT.containsKey(c) && windowCounts.get(c).intValue() == dictT.get(c).intValue()) {
                formed++;
            }
            
            while (left <= right && formed == required) {
                c = s.charAt(left);
                if (ans[0] == -1 || right - left + 1 < ans[0]) {
                    ans[0] = right - left + 1;
                    ans[1] = left;
                    ans[2] = right;
                }
                
                windowCounts.put(c, windowCounts.get(c) - 1);
                if (dictT.containsKey(c) && windowCounts.get(c) < dictT.get(c)) {
                    formed--;
                }
                left++;
            }
            right++;
        }
        
        return ans[0] == -1 ? "" : s.substring(ans[1], ans[2] + 1);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(S + T)` với S, T là chiều dài của hai chuỗi, mỗi ký tự bị thăm tối đa 2 lần (left và right).
- **Space Complexity:** `O(S + T)` không gian cho Map.

---

## 11. Two Sum

**Đề bài chi tiết:**
Cho một mảng các số nguyên `nums` và một số nguyên `target`, trả về chỉ số (index) của hai số sao cho tổng của chúng bằng `target`.
Bạn có thể giả định rằng mỗi đầu vào sẽ có chính xác một nghiệm và bạn không thể sử dụng cùng một phần tử hai lần.
Bạn có thể trả về câu trả lời theo bất kỳ thứ tự nào.

**Phân tích thuật toán:**
Thay vì duyệt qua từng cặp bằng hai vòng lặp lồng nhau mất `O(N^2)`, ta dùng một Hash Map. 
Trong đó:
- Key: Giá trị của phần tử trong mảng.
- Value: Chỉ số (index) của phần tử đó.
Ta duyệt qua mảng, tại mỗi phần tử `nums[i]`, kiểm tra xem phần bù `target - nums[i]` đã xuất hiện trong Hash Map chưa. Nếu có, ta lấy index của phần bù và `i` làm kết quả. Nếu chưa, ta đưa `nums[i]` và `i` vào Hash Map.

**Mã nguồn Java:**
```java
import java.util.*;

public class TwoSum {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (map.containsKey(complement)) {
                return new int[] { map.get(complement), i };
            }
            map.put(nums[i], i);
        }
        throw new IllegalArgumentException("No two sum solution");
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N)` với N là số lượng phần tử của mảng, do mỗi phép tra cứu trong Hash Map mất `O(1)` trung bình.
- **Space Complexity:** `O(N)` để lưu trữ các phần tử vào Hash Map.

---

## 12. Contains Duplicate II

**Đề bài chi tiết:**
Cho một mảng số nguyên `nums` và một số nguyên `k`, trả về `true` nếu tồn tại hai chỉ số `i` và `j` khác nhau trong mảng sao cho `nums[i] == nums[j]` và `Math.abs(i - j) <= k`. Ngược lại, trả về `false`.

**Phân tích thuật toán:**
Ta sử dụng Hash Map để lưu vết các phần tử đã duyệt.
- Key: Giá trị phần tử `nums[i]`.
- Value: Chỉ số xuất hiện gần nhất của nó (index `i`).
Khi duyệt mảng, nếu `nums[i]` đã có trong Hash Map, ta kiểm tra xem khoảng cách `i - map.get(nums[i]) <= k`. Nếu đúng thì thỏa mãn điều kiện và trả về `true`. Nếu không, cập nhật lại chỉ số mới cho `nums[i]` trong Map vì ta cần khoảng cách nhỏ nhất có thể cho các lần kiểm tra tiếp theo.

**Mã nguồn Java:**
```java
import java.util.*;

public class ContainsDuplicateII {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(nums[i]) && i - map.get(nums[i]) <= k) {
                return true;
            }
            map.put(nums[i], i);
        }
        return false;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N)` vì chỉ cần duyệt qua mảng `nums` một lần.
- **Space Complexity:** `O(N)` không gian lưu trữ cho Hash Map.

---

## 13. Intersection of Two Arrays II

**Đề bài chi tiết:**
Cho hai mảng số nguyên `nums1` và `nums2`, trả về một mảng chứa giao của hai mảng này. Mỗi phần tử trong kết quả phải xuất hiện số lần bằng với số lần nó xuất hiện trong cả hai mảng (nói cách khác, lấy min tần suất của cả hai mảng). Bạn có thể trả về kết quả theo bất kỳ thứ tự nào.

**Phân tích thuật toán:**
1. Duyệt mảng thứ nhất và đếm tần suất xuất hiện của từng số bằng Hash Map (`Map<Integer, Integer>`).
2. Duyệt qua mảng thứ hai, đối với mỗi số, nếu nó có trong Hash Map và số lần đếm > 0, ta thêm số đó vào kết quả và giảm số lần đếm trong Hash Map đi 1.

**Mã nguồn Java:**
```java
import java.util.*;

public class IntersectionOfTwoArraysII {
    public int[] intersect(int[] nums1, int[] nums2) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int num : nums1) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }
        
        List<Integer> list = new ArrayList<>();
        for (int num : nums2) {
            if (map.getOrDefault(num, 0) > 0) {
                list.add(num);
                map.put(num, map.get(num) - 1);
            }
        }
        
        int[] result = new int[list.size()];
        for (int i = 0; i < list.size(); i++) {
            result[i] = list.get(i);
        }
        return result;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(M + N)` với M và N là độ dài của hai mảng.
- **Space Complexity:** `O(min(M, N))` nếu ta xây dựng Hash Map trên mảng có kích thước nhỏ hơn (trong ví dụ trên, không gian là `O(M)`).

---

## 14. First Unique Character in a String

**Đề bài chi tiết:**
Cho một chuỗi `s`, tìm ký tự đầu tiên không lặp lại trong chuỗi và trả về chỉ số (index) của nó. Nếu nó không tồn tại, trả về `-1`.

**Phân tích thuật toán:**
Có thể dùng mảng kích thước 26 hoặc Hash Map để đếm tần suất.
1. Quét chuỗi một lần để đếm số lần xuất hiện của mỗi ký tự bằng Hash Map (`Map<Character, Integer>`).
2. Quét chuỗi lại lần thứ hai, đối với mỗi ký tự, kiểm tra số lần xuất hiện của nó. Ký tự đầu tiên có tần suất bằng 1 chính là kết quả, trả về chỉ số của nó.

**Mã nguồn Java:**
```java
import java.util.*;

public class FirstUniqueCharacter {
    public int firstUniqChar(String s) {
        Map<Character, Integer> map = new HashMap<>();
        for (char c : s.toCharArray()) {
            map.put(c, map.getOrDefault(c, 0) + 1);
        }
        
        for (int i = 0; i < s.length(); i++) {
            if (map.get(s.charAt(i)) == 1) {
                return i;
            }
        }
        return -1;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N)` với N là chiều dài của chuỗi `s` (duyệt 2 lần).
- **Space Complexity:** `O(1)` (Tối đa 26 ký tự chữ cái tiếng Anh in thường, độ lớn Hash Map là hằng số).

---

## 15. Longest Palindrome

**Đề bài chi tiết:**
Cho một chuỗi `s` bao gồm các chữ cái in hoa và in thường, hãy trả về độ dài của chuỗi palindrome (chuỗi đối xứng) dài nhất có thể được xây dựng bằng những chữ cái đó.
Các chữ cái phân biệt chữ hoa, chữ thường (ví dụ `"Aa"` không được coi là palindrome).

**Phân tích thuật toán:**
Sử dụng HashSet để theo dõi các ký tự có thể ghép thành cặp. 
Duyệt qua chuỗi, nếu ký tự đã có trong Set, ta xoá nó và cộng độ dài thêm 2 (do ghép được 1 cặp). Nếu chưa có, ta thêm vào Set. Cuối cùng, nếu Set không rỗng (nghĩa là còn ít nhất 1 ký tự lẻ), ta có thể cộng thêm 1 vào độ dài (để đặt ký tự này ở trung tâm của palindrome).

**Mã nguồn Java:**
```java
import java.util.*;

public class LongestPalindrome {
    public int longestPalindrome(String s) {
        Set<Character> set = new HashSet<>();
        int length = 0;
        
        for (char c : s.toCharArray()) {
            if (set.contains(c)) {
                set.remove(c);
                length += 2;
            } else {
                set.add(c);
            }
        }
        
        if (!set.isEmpty()) {
            length += 1;
        }
        
        return length;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N)` duyệt chuỗi một lần.
- **Space Complexity:** `O(1)` do Set lưu trữ tối đa 52 ký tự alphabet.

---

## 16. Longest Harmonious Subsequence

**Đề bài chi tiết:**
Một chuỗi (subsequence) hài hòa là chuỗi con (không cần liên tiếp) trong đó độ chênh lệch giữa giá trị lớn nhất và giá trị nhỏ nhất của nó chính xác là 1.
Cho một mảng các số nguyên `nums`, hãy tìm độ dài của chuỗi hài hòa dài nhất có thể tạo ra bằng cách lấy một số hoặc tất cả các phần tử của mảng.

**Phân tích thuật toán:**
Dùng Hash Map đếm tần suất xuất hiện của tất cả các phần tử trong mảng.
Sau đó, duyệt qua các khoá (keys) của Hash Map. Với mỗi phần tử `k`, kiểm tra xem `k + 1` có tồn tại trong Map không. Nếu có, độ dài của chuỗi hài hòa tạo bởi `k` và `k + 1` sẽ là `map.get(k) + map.get(k + 1)`. Ta cập nhật giá trị max với tổng này sau mỗi lần kiểm tra.

**Mã nguồn Java:**
```java
import java.util.*;

public class LongestHarmoniousSubsequence {
    public int findLHS(int[] nums) {
        Map<Integer, Integer> countMap = new HashMap<>();
        for (int num : nums) {
            countMap.put(num, countMap.getOrDefault(num, 0) + 1);
        }
        
        int maxLength = 0;
        for (int key : countMap.keySet()) {
            if (countMap.containsKey(key + 1)) {
                maxLength = Math.max(maxLength, countMap.get(key) + countMap.get(key + 1));
            }
        }
        
        return maxLength;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N)` để đếm tần suất và duyệt qua tối đa N key trong Hash Map.
- **Space Complexity:** `O(N)` không gian cho Map.

---

## 17. Maximum Size Subarray Sum Equals k

**Đề bài chi tiết:**
Cho một mảng số nguyên `nums` và một số nguyên `k`, hãy tìm chiều dài lớn nhất của một mảng con liên tiếp mà tổng các phần tử của nó bằng `k`. Nếu không có mảng con nào, trả về 0.

**Phân tích thuật toán:**
Ta dùng kỹ thuật Prefix Sum kết hợp với Hash Map. Map dùng để lưu giá trị Prefix Sum và chỉ số (index) đầu tiên mà Prefix Sum đạt được (giúp tối đa hóa chiều dài).
Tại mỗi bước `i`, tính `prefixSum`. 
- Nếu `prefixSum == k`, ta có mảng con từ đầu mảng đến `i` (độ dài `i + 1`). 
- Nếu `prefixSum - k` tồn tại trong Map tại chỉ số `j`, ta có mảng con từ `j + 1` đến `i` có tổng bằng `k` và độ dài là `i - j`.
Chỉ đưa `prefixSum` vào Map nếu nó chưa từng xuất hiện.

**Mã nguồn Java:**
```java
import java.util.*;

public class MaxSizeSubarraySumEqualsK {
    public int maxSubArrayLen(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        int prefixSum = 0;
        int maxLength = 0;
        
        for (int i = 0; i < nums.length; i++) {
            prefixSum += nums[i];
            
            if (prefixSum == k) {
                maxLength = i + 1;
            }
            
            if (map.containsKey(prefixSum - k)) {
                maxLength = Math.max(maxLength, i - map.get(prefixSum - k));
            }
            
            if (!map.containsKey(prefixSum)) {
                map.put(prefixSum, i);
            }
        }
        
        return maxLength;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N)`
- **Space Complexity:** `O(N)`

---

## 18. Contiguous Array

**Đề bài chi tiết:**
Cho một mảng nhị phân `nums` (chỉ chứa 0 và 1), hãy tìm độ dài lớn nhất của một mảng con liên tiếp có số lượng 0 và 1 bằng nhau.

**Phân tích thuật toán:**
Quy bài toán về Prefix Sum bằng cách coi giá trị `0` là `-1`. Lúc này, ta cần tìm mảng con có tổng bằng `0`.
Dùng Hash Map lưu: Key là `prefixSum`, Value là chỉ số đầu tiên đạt được giá trị tổng này.
Khởi tạo Map với `map.put(0, -1)` để xử lý mảng bắt đầu từ index 0 hợp lệ. Khi duyệt, nếu gặp `prefixSum` đã tồn tại trong Map, độ dài mảng con sẽ là `i - map.get(prefixSum)`.

**Mã nguồn Java:**
```java
import java.util.*;

public class ContiguousArray {
    public int findMaxLength(int[] nums) {
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, -1);
        int prefixSum = 0;
        int maxLength = 0;
        
        for (int i = 0; i < nums.length; i++) {
            prefixSum += (nums[i] == 1 ? 1 : -1);
            
            if (map.containsKey(prefixSum)) {
                maxLength = Math.max(maxLength, i - map.get(prefixSum));
            } else {
                map.put(prefixSum, i);
            }
        }
        
        return maxLength;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N)`
- **Space Complexity:** `O(N)`

---

## 19. Valid Sudoku

**Đề bài chi tiết:**
Xác định xem một bảng Sudoku kích thước 9x9 có hợp lệ hay không. Chỉ cần các ô đã điền là hợp lệ theo các quy tắc sau:
1. Mỗi hàng phải chứa các số từ `1-9` không bị lặp lại.
2. Mỗi cột phải chứa các số từ `1-9` không bị lặp lại.
3. Mỗi hộp con kích thước `3x3` của lưới 9x9 phải chứa các số từ `1-9` không lặp lại.

**Phân tích thuật toán:**
Dùng HashSet để lưu lại các số đã gặp trong cùng một hàng, một cột, hoặc một ô vuông 3x3.
Mỗi ô không rỗng sẽ được kiểm tra với 3 chuỗi đại diện cho Hàng, Cột, và Box:
- Hàng: `i + "(" + number + ")"`
- Cột: `"(" + number + ")" + j`
- Box: `i/3 + "(" + number + ")" + j/3`
Nếu `add` thất bại (tức là đã tồn tại), thì bảng không hợp lệ.

**Mã nguồn Java:**
```java
import java.util.*;

public class ValidSudoku {
    public boolean isValidSudoku(char[][] board) {
        Set<String> seen = new HashSet<>();
        
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                char number = board[i][j];
                if (number != '.') {
                    String b = "(" + number + ")";
                    if (!seen.add(i + b) || 
                        !seen.add(b + j) || 
                        !seen.add(i / 3 + b + j / 3)) {
                        return false;
                    }
                }
            }
        }
        return true;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(1)` vì bảng có kích thước cố định 9x9.
- **Space Complexity:** `O(1)`

---

## 20. H-Index

**Đề bài chi tiết:**
Cho một mảng các số nguyên `citations` đại diện cho số lượng trích dẫn mà một nhà nghiên cứu nhận được cho mỗi bài báo của họ. Tính H-Index của nhà nghiên cứu đó. H-Index là số lớn nhất `h` sao cho nhà nghiên cứu có ít nhất `h` bài báo được trích dẫn ít nhất `h` lần mỗi bài.

**Phân tích thuật toán:**
Bài toán có thể giải bằng Counting Sort (Bucket Sort) hay Hash Map. Ta dùng mảng đếm tần suất như một Hash Map giới hạn giá trị.
- Tạo mảng `count` kích thước `N + 1`. Các bài báo có số trích dẫn `> N` sẽ được gộp vào `count[N]` (vì H-Index không thể vượt quá `N`).
- Duyệt qua `citations`, cộng số lượng vào mảng `count`.
- Duyệt từ `N` về `0`, cộng dồn tổng số lượng bài báo. Nếu tổng số lượng `>= i`, thì `i` chính là H-Index lớn nhất.

**Mã nguồn Java:**
```java
public class HIndex {
    public int hIndex(int[] citations) {
        int n = citations.length;
        int[] count = new int[n + 1]; // Hoạt động như một Hash Map thu gọn
        
        for (int c : citations) {
            if (c >= n) {
                count[n]++;
            } else {
                count[c]++;
            }
        }
        
        int total = 0;
        for (int i = n; i >= 0; i--) {
            total += count[i];
            if (total >= i) {
                return i;
            }
        }
        return 0;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N)` với N là số lượng bài báo.
- **Space Complexity:** `O(N)` mảng `count` kích thước N + 1.

---

## 21. Insert Delete GetRandom O(1)

**Đề bài chi tiết:**
Thiết kế một cấu trúc dữ liệu thực hiện các thao tác sau với độ phức tạp thời gian trung bình là `O(1)`:
- `insert(val)`: Chèn một phần tử `val` vào tập hợp nếu chưa tồn tại.
- `remove(val)`: Xóa một phần tử `val` khỏi tập hợp nếu có tồn tại.
- `getRandom()`: Lấy ngẫu nhiên một phần tử từ tập hợp hiện tại. Mỗi phần tử phải có xác suất được chọn bằng nhau.

**Phân tích thuật toán:**
Để `getRandom` đạt `O(1)`, ta có thể lưu các phần tử vào một mảng (hoặc `ArrayList` trong Java). Random index trên mảng là `O(1)`.
Tuy nhiên, để `insert` và `remove` trong mảng là `O(1)`, ta cần một Hash Map để lưu vị trí (index) của phần tử. 
Khi `remove(val)`, ta lấy index của nó từ Map, hoán đổi phần tử đó với phần tử cuối cùng của `ArrayList`, sau đó xóa phần tử cuối cùng, và cập nhật lại index trong Map cho phần tử vừa được hoán đổi.

**Mã nguồn Java:**
```java
import java.util.*;

public class RandomizedSet {
    private Map<Integer, Integer> dict;
    private List<Integer> list;
    private Random rand;

    public RandomizedSet() {
        dict = new HashMap<>();
        list = new ArrayList<>();
        rand = new Random();
    }
    
    public boolean insert(int val) {
        if (dict.containsKey(val)) return false;
        dict.put(val, list.size());
        list.add(val);
        return true;
    }
    
    public boolean remove(int val) {
        if (!dict.containsKey(val)) return false;
        
        int index = dict.get(val);
        int lastElement = list.get(list.size() - 1);
        
        // Hoán đổi với phần tử cuối
        list.set(index, lastElement);
        dict.put(lastElement, index);
        
        // Xóa phần tử cuối
        list.remove(list.size() - 1);
        dict.remove(val);
        
        return true;
    }
    
    public int getRandom() {
        return list.get(rand.nextInt(list.size()));
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(1)` trung bình cho mỗi thao tác.
- **Space Complexity:** `O(N)` với N là số lượng phần tử lưu trong cấu trúc.

---

## 22. LRU Cache

**Đề bài chi tiết:**
Thiết kế một cấu trúc dữ liệu cho Least Recently Used (LRU) Cache.
- `LRUCache(int capacity)` Khởi tạo cache với sức chứa `capacity`.
- `int get(int key)` Trả về giá trị của `key` nếu tồn tại, ngược lại trả về `-1`.
- `void put(int key, int value)` Cập nhật giá trị của `key` nếu có tồn tại, hoặc chèn cặp `key-value` vào nếu không. Nếu thao tác này làm số lượng khóa vượt quá `capacity`, hãy loại bỏ khóa ít được sử dụng gần đây nhất (LRU).
Yêu cầu cả 2 thao tác `get` và `put` đều phải chạy trong `O(1)`.

**Phân tích thuật toán:**
Dùng `HashMap` kết hợp với Danh sách liên kết đôi (`Doubly Linked List`). 
`HashMap` giúp truy cập `node` trong danh sách liên kết trong `O(1)`.
`Doubly Linked List` giúp di chuyển một node lên đầu danh sách (dấu hiệu vừa được dùng) và xóa node ở cuối danh sách (LRU) trong `O(1)`.
Trong Java, có thể tự cài đặt hoặc dùng `LinkedHashMap` với cờ `accessOrder = true`. Dưới đây là cách tự cài đặt cơ bản.

**Mã nguồn Java:**
```java
import java.util.*;

class Node {
    int key, value;
    Node prev, next;
    Node(int k, int v) { key = k; value = v; }
}

public class LRUCache {
    private int capacity;
    private Map<Integer, Node> map;
    private Node head, tail;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        map = new HashMap<>();
        head = new Node(0, 0);
        tail = new Node(0, 0);
        head.next = tail;
        tail.prev = head;
    }
    
    public int get(int key) {
        if (map.containsKey(key)) {
            Node node = map.get(key);
            remove(node);
            insert(node);
            return node.value;
        }
        return -1;
    }
    
    public void put(int key, int value) {
        if (map.containsKey(key)) {
            remove(map.get(key));
        }
        if (map.size() == capacity) {
            remove(tail.prev);
        }
        insert(new Node(key, value));
    }
    
    private void remove(Node node) {
        map.remove(node.key);
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }
    
    private void insert(Node node) {
        map.put(node.key, node);
        Node headNext = head.next;
        head.next = node;
        node.prev = head;
        headNext.prev = node;
        node.next = headNext;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(1)` cho `get` và `put`.
- **Space Complexity:** `O(capacity)` cho Hash Map và Doubly Linked List.

---

## 23. LFU Cache

**Đề bài chi tiết:**
Thiết kế và cài đặt cấu trúc dữ liệu Least Frequently Used (LFU) cache.
- `LFUCache(int capacity)`
- `int get(int key)`: Trả về giá trị nếu khóa tồn tại, ngược lại trả về `-1`.
- `void put(int key, int value)`: Cập nhật hoặc chèn thêm cặp khóa-giá trị. Khi cache đạt dung lượng tối đa, vô hiệu hóa phần tử ít được sử dụng thường xuyên nhất trước khi chèn mới. Nếu có nhiều phần tử có cùng tần suất sử dụng, xóa phần tử Least Recently Used (LRU) trong số chúng. Cả hai thao tác phải là `O(1)`.

**Phân tích thuật toán:**
Dùng 2 Hash Map:
- `keyToVal`: Lưu khóa và giá trị (kèm theo biến tần suất `keyToFreq`).
- `freqToLRU`: Map từ tần suất (frequency) sang một danh sách liên kết đôi (có thể là `LinkedHashSet` trong Java để tối ưu `O(1)`) lưu các khóa có cùng tần suất đó.
Đồng thời lưu trữ biến `minFreq` để biết tần suất nhỏ nhất hiện tại.

**Mã nguồn Java:**
```java
import java.util.*;

public class LFUCache {
    private int capacity, minFreq;
    private Map<Integer, Integer> keyToVal;
    private Map<Integer, Integer> keyToFreq;
    private Map<Integer, LinkedHashSet<Integer>> freqToLRU;

    public LFUCache(int capacity) {
        this.capacity = capacity;
        this.minFreq = 0;
        keyToVal = new HashMap<>();
        keyToFreq = new HashMap<>();
        freqToLRU = new HashMap<>();
    }
    
    public int get(int key) {
        if (!keyToVal.containsKey(key)) return -1;
        int freq = keyToFreq.get(key);
        keyToFreq.put(key, freq + 1);
        freqToLRU.get(freq).remove(key);
        
        if (freq == minFreq && freqToLRU.get(freq).isEmpty()) {
            minFreq++;
        }
        
        freqToLRU.computeIfAbsent(freq + 1, k -> new LinkedHashSet<>()).add(key);
        return keyToVal.get(key);
    }
    
    public void put(int key, int value) {
        if (capacity <= 0) return;
        
        if (keyToVal.containsKey(key)) {
            keyToVal.put(key, value);
            get(key); // cập nhật frequency
            return;
        }
        
        if (keyToVal.size() >= capacity) {
            int evict = freqToLRU.get(minFreq).iterator().next();
            freqToLRU.get(minFreq).remove(evict);
            keyToVal.remove(evict);
            keyToFreq.remove(evict);
        }
        
        keyToVal.put(key, value);
        keyToFreq.put(key, 1);
        minFreq = 1;
        freqToLRU.computeIfAbsent(1, k -> new LinkedHashSet<>()).add(key);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(1)` cho `get` và `put` trung bình.
- **Space Complexity:** `O(capacity)`.

---

## 24. Find Duplicate File in System

**Đề bài chi tiết:**
Cho một danh sách các chuỗi thư mục, định dạng là `"Đường_dẫn Tên_file(Nội_dung_file)"`.
Hãy trả về một danh sách các nhóm đường dẫn file (mỗi nhóm chứa danh sách đường dẫn tới các file bị lặp lại về mặt nội dung).

**Phân tích thuật toán:**
Dùng Hash Map để nhóm các file có cùng nội dung:
- Key: Chuỗi nội dung file.
- Value: Danh sách đường dẫn đầy đủ tới file đó (Path + Tên file).
Ta duyệt qua chuỗi thư mục, phân tích và lấy ra path, file, nội dung. Cuối cùng, lọc các values trong Map có danh sách lớn hơn 1 để trả về.

**Mã nguồn Java:**
```java
import java.util.*;

public class FindDuplicateFile {
    public List<List<String>> findDuplicate(String[] paths) {
        Map<String, List<String>> contentMap = new HashMap<>();
        
        for (String p : paths) {
            String[] parts = p.split(" ");
            String root = parts[0];
            
            for (int i = 1; i < parts.length; i++) {
                int openParen = parts[i].indexOf('(');
                String fileName = parts[i].substring(0, openParen);
                String content = parts[i].substring(openParen + 1, parts[i].length() - 1);
                
                String fullPath = root + "/" + fileName;
                contentMap.computeIfAbsent(content, k -> new ArrayList<>()).add(fullPath);
            }
        }
        
        List<List<String>> result = new ArrayList<>();
        for (List<String> group : contentMap.values()) {
            if (group.size() > 1) {
                result.add(group);
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N * C)` với N là số chuỗi thư mục và C là độ dài trung bình của đường dẫn/nội dung.
- **Space Complexity:** `O(N * C)` để lưu vào Hash Map.

---

## 25. Brick Wall

**Đề bài chi tiết:**
Một bức tường gạch hình chữ nhật gồm nhiều hàng gạch. Các viên gạch trong cùng một hàng có chiều rộng khác nhau nhưng chiều cao bằng nhau.
Hãy vẽ một đường thẳng đứng xuyên qua bức tường ít gạch nhất. Bạn không thể vẽ đường cắt ở hai cạnh bên của bức tường.

**Phân tích thuật toán:**
Thay vì tìm nơi cắt qua ít gạch nhất, ta tìm nơi cắt qua *nhiều cạnh (mép)* của các viên gạch nhất (vì xuyên qua mép sẽ không cắt gạch).
Dùng Hash Map đếm số lần xuất hiện các vị trí cạnh gạch. 
- Key: Khoảng cách từ đầu hàng tới mép gạch.
- Value: Số lượng hàng có khe hở tại khoảng cách này.
Vị trí mép gạch = tổng chiều rộng của các viên gạch từ trái sang phải.
Số viên gạch bị cắt = Tổng số hàng - Số khe hở lớn nhất.

**Mã nguồn Java:**
```java
import java.util.*;

public class BrickWall {
    public int leastBricks(List<List<Integer>> wall) {
        Map<Integer, Integer> edgeCounts = new HashMap<>();
        int maxEdges = 0;
        
        for (List<Integer> row : wall) {
            int edgePosition = 0;
            // Không tính viên gạch cuối cùng (mép của bức tường)
            for (int i = 0; i < row.size() - 1; i++) {
                edgePosition += row.get(i);
                int count = edgeCounts.getOrDefault(edgePosition, 0) + 1;
                edgeCounts.put(edgePosition, count);
                maxEdges = Math.max(maxEdges, count);
            }
        }
        
        return wall.size() - maxEdges;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(TotalBricks)` - thời gian duyệt qua tất cả viên gạch.
- **Space Complexity:** `O(Width)` - hoặc số lượng khe hở duy nhất có thể có (phụ thuộc vào Hash Map).

---

## 26. Longest Consecutive Sequence

**Đề bài chi tiết:**
Cho một mảng chưa sắp xếp các số nguyên, hãy tìm chiều dài của dãy số nguyên liên tiếp dài nhất.
Yêu cầu phải viết thuật toán chạy trong thời gian `O(N)`.

**Phân tích thuật toán:**
Nếu sắp xếp thì mất `O(N log N)`. 
Để tối ưu, ta cho tất cả các phần tử vào một HashSet.
Duyệt qua các số trong set, số `x` chỉ được coi là "điểm bắt đầu" của một dãy nếu `x - 1` KHÔNG tồn tại trong Set. Nếu `x` là điểm bắt đầu, ta lặp tăng `x` dần dần để đếm chiều dài của chuỗi liên tiếp và cập nhật chiều dài lớn nhất.

**Mã nguồn Java:**
```java
import java.util.*;

public class LongestConsecutiveSequence {
    public int longestConsecutive(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int n : nums) set.add(n);
        
        int longest = 0;
        for (int n : set) {
            if (!set.contains(n - 1)) {
                int currentNum = n;
                int currentStreak = 1;
                
                while (set.contains(currentNum + 1)) {
                    currentNum += 1;
                    currentStreak += 1;
                }
                
                longest = Math.max(longest, currentStreak);
            }
        }
        return longest;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N)` vì mỗi phần tử chỉ được chèn vào tập hợp 1 lần, và đoạn `while` chỉ chạy qua chuỗi đúng 1 lần (các số không phải bắt đầu sẽ bị bỏ qua).
- **Space Complexity:** `O(N)` cho HashSet.

---

## 27. Number of Boomerangs

**Đề bài chi tiết:**
Cho n điểm trên mặt phẳng. Tìm số lượng các cặp điểm tạo thành "Boomerang". Một boomerang là một bộ ba điểm `(i, j, k)` sao cho khoảng cách giữa `i` và `j` bằng với khoảng cách giữa `i` và `k` (độ dài theo Euclidean). Các điểm có phân biệt thứ tự.

**Phân tích thuật toán:**
Duyệt qua từng điểm `i` (đóng vai trò điểm giữa). Đối với mỗi điểm `i`, dùng một Hash Map đếm số lượng điểm `j` có cùng khoảng cách (bình phương khoảng cách để tránh số thực) tới `i`.
Nếu có `k` điểm cùng khoảng cách, số lượng boomerang tạo được là `k * (k - 1)` (chỉnh hợp chọn 2 từ `k`). Cần khởi tạo lại Map mỗi khi đổi điểm `i`.

**Mã nguồn Java:**
```java
import java.util.*;

public class NumberOfBoomerangs {
    public int numberOfBoomerangs(int[][] points) {
        int res = 0;
        Map<Integer, Integer> map = new HashMap<>();
        
        for (int i = 0; i < points.length; i++) {
            for (int j = 0; j < points.length; j++) {
                if (i == j) continue;
                int d = getDistance(points[i], points[j]);
                map.put(d, map.getOrDefault(d, 0) + 1);
            }
            
            for (int val : map.values()) {
                res += val * (val - 1);
            }
            map.clear();
        }
        return res;
    }
    
    private int getDistance(int[] a, int[] b) {
        int dx = a[0] - b[0];
        int dy = a[1] - b[1];
        return dx * dx + dy * dy;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N^2)` vòng lặp lồng nhau duyệt qua mọi cặp điểm.
- **Space Complexity:** `O(N)` cho Hash Map (lưu tối đa N khoảng cách tại mỗi điểm i).

---

## 28. Max Points on a Line

**Đề bài chi tiết:**
Cho một mảng `points` trong đó mỗi phần tử đại diện cho một điểm trên mặt phẳng toạ độ 2D. Hãy tìm số lượng điểm tối đa nằm trên cùng một đường thẳng.

**Phân tích thuật toán:**
Cố định một điểm gốc `i`, đường thẳng đi qua `i` được xác định hoàn toàn bởi hệ số góc (slope) với điểm `j`.
Dùng Hash Map để nhóm các điểm `j` có cùng hệ số góc so với điểm gốc `i`.
Do dùng số thực (`double`) cho hệ số góc dễ dẫn đến lỗi sai số epsilon, ta biểu diễn hệ số góc dưới dạng phân số tối giản `(dy, dx)` (chia cho UCLN của chúng).
- Key: Phân số tối giản `dy + "/" + dx`.
- Value: Số điểm nằm trên cùng phương.

**Mã nguồn Java:**
```java
import java.util.*;

public class MaxPointsOnALine {
    public int maxPoints(int[][] points) {
        int n = points.length;
        if (n < 3) return n;
        int maxPoints = 0;
        
        for (int i = 0; i < n; i++) {
            Map<String, Integer> slopes = new HashMap<>();
            int duplicates = 1;
            int currentMax = 0;
            
            for (int j = i + 1; j < n; j++) {
                int dx = points[j][0] - points[i][0];
                int dy = points[j][1] - points[i][1];
                
                if (dx == 0 && dy == 0) {
                    duplicates++;
                    continue;
                }
                
                int g = gcd(dx, dy);
                dx /= g;
                dy /= g;
                
                String slope = dy + "/" + dx;
                slopes.put(slope, slopes.getOrDefault(slope, 0) + 1);
                currentMax = Math.max(currentMax, slopes.get(slope));
            }
            maxPoints = Math.max(maxPoints, currentMax + duplicates);
        }
        return maxPoints;
    }
    
    private int gcd(int a, int b) {
        return b == 0 ? a : gcd(b, a % b);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(N^2 log(Max_Coordinate))` do tính UCLN bằng Euclid trên mỗi cặp.
- **Space Complexity:** `O(N)` do Hash Map chứa hệ số góc tương đối của các điểm.

---

## 29. Fractional Recurring Decimal

**Đề bài chi tiết:**
Cho tử số và mẫu số của một phân số biểu diễn bằng số nguyên. Trả về biểu diễn chuỗi của số thập phân đó.
Nếu phần thập phân có lặp lại, đưa phần lặp lại vào trong ngoặc đơn `()`.

**Phân tích thuật toán:**
Chia ra phần nguyên và phần thập phân.
Để tính phần thập phân lặp (chu kỳ), ta lưu vết tất cả các số dư (remainder). 
Dùng Hash Map để lưu:
- Key: số dư.
- Value: vị trí trong chuỗi StringBuilder khi số dư này xuất hiện.
Nếu gặp một số dư đã tồn tại trong Map, nghĩa là ta bắt đầu chu kỳ lặp, chèn ngoặc `(` vào vị trí đã lưu và `)` vào cuối.

**Mã nguồn Java:**
```java
import java.util.*;

public class FractionalRecurringDecimal {
    public String fractionToDecimal(int numerator, int denominator) {
        if (numerator == 0) return "0";
        StringBuilder sb = new StringBuilder();
        
        if (numerator < 0 ^ denominator < 0) sb.append("-");
        
        long n = Math.abs((long)numerator);
        long d = Math.abs((long)denominator);
        
        sb.append(n / d);
        long remainder = n % d;
        if (remainder == 0) return sb.toString();
        
        sb.append(".");
        Map<Long, Integer> map = new HashMap<>();
        
        while (remainder != 0) {
            if (map.containsKey(remainder)) {
                sb.insert(map.get(remainder), "(");
                sb.append(")");
                break;
            }
            map.put(remainder, sb.length());
            remainder *= 10;
            sb.append(remainder / d);
            remainder %= d;
        }
        
        return sb.toString();
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(L)` với L là độ dài phần nguyên và chu kỳ số thập phân.
- **Space Complexity:** `O(L)` không gian để lưu trữ số dư.

---

## 30. Implement Trie (Prefix Tree)

**Đề bài chi tiết:**
Cài đặt Trie (Cây Tiền Tố). 
- `Trie()`: Khởi tạo đối tượng cây.
- `void insert(String word)`: Chèn một chuỗi `word` vào cây.
- `boolean search(String word)`: Trả về true nếu chuỗi `word` nằm trong cây.
- `boolean startsWith(String prefix)`: Trả về true nếu có một chuỗi bất kỳ trong cây bắt đầu bằng tiền tố `prefix`.

**Phân tích thuật toán:**
Một nút trong Trie thường chứa các liên kết tới nút con. Thay vì dùng mảng tĩnh (ví dụ `TrieNode[26]` chỉ cho chữ thường), dùng Hash Map (`Map<Character, TrieNode>`) để có thể lưu trữ đa dạng loại ký tự (Unicode) và giảm thiểu bộ nhớ cấp phát tĩnh dư thừa.
Quá trình chèn hay tìm kiếm đều di chuyển trên Hash Map của các Nút từ trên gốc xuống.

**Mã nguồn Java:**
```java
import java.util.*;

class TrieNode {
    Map<Character, TrieNode> children;
    boolean isEndOfWord;

    public TrieNode() {
        children = new HashMap<>();
        isEndOfWord = false;
    }
}

public class Trie {
    private TrieNode root;

    public Trie() {
        root = new TrieNode();
    }
    
    public void insert(String word) {
        TrieNode current = root;
        for (char c : word.toCharArray()) {
            current.children.putIfAbsent(c, new TrieNode());
            current = current.children.get(c);
        }
        current.isEndOfWord = true;
    }
    
    public boolean search(String word) {
        TrieNode current = root;
        for (char c : word.toCharArray()) {
            if (!current.children.containsKey(c)) return false;
            current = current.children.get(c);
        }
        return current.isEndOfWord;
    }
    
    public boolean startsWith(String prefix) {
        TrieNode current = root;
        for (char c : prefix.toCharArray()) {
            if (!current.children.containsKey(c)) return false;
            current = current.children.get(c);
        }
        return true;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** `O(L)` cho mỗi thao tác, với L là độ dài của chuỗi. Tìm kiếm Hash Map tại mỗi node là `O(1)`.
- **Space Complexity:** `O(T * L)` với T là tổng số lượng từ và L là độ dài của từ, lượng bộ nhớ cho Hash Map linh động hơn mảng tĩnh.
