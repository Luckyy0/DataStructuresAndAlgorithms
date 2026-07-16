# Companion Problems: 02-Problem-Solving-Process

Tài liệu này cung cấp **30 bài toán thực hành** rèn luyện kỹ năng giải quyết vấn đề (Problem Solving). Mỗi bài bao gồm Đề bài chi tiết, Phân tích hướng giải quyết (Algorithm Analysis) và Mã nguồn Java.

---

## 1. Two Sum
**Đề bài chi tiết:** Cho một mảng số nguyên `nums` và một số nguyên `target`. Hãy trả về chỉ số (indices) của 2 số sao cho tổng của chúng bằng `target`. Giả định luôn có đúng 1 đáp án và không sử dụng cùng một phần tử 2 lần.
**Phân tích thuật toán:** Hai vòng lặp lồng nhau mất $\mathcal{O}(N^2)$. Giải pháp tối ưu: duyệt mảng 1 lần. Tại mỗi số `nums[i]`, phần cần tìm (complement) là `target - nums[i]`. Tra cứu trong một cấu trúc dữ liệu lưu trữ các số đã duyệt qua. `HashMap` cho phép tra cứu trong $\mathcal{O}(1)$.
**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;
public class TwoSum {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int comp = target - nums[i];
            if (map.containsKey(comp)) return new int[] {map.get(comp), i};
            map.put(nums[i], i);
        }
        return new int[]{};
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 2. Best Time to Buy and Sell Stock
**Đề bài chi tiết:** Cho một mảng `prices` nơi `prices[i]` là giá cổ phiếu vào ngày thứ `i`. Hãy tối đa hóa lợi nhuận bằng cách chọn 1 ngày mua và 1 ngày bán trong tương lai. Nếu không có lãi, trả về 0.
**Phân tích thuật toán:** Giao dịch chỉ có thể xảy ra khi Ngày Mua < Ngày Bán. Thay vì sinh ra mọi cặp Mua-Bán tốn $\mathcal{O}(N^2)$, ta duyệt mảng 1 lần. Tại mỗi ngày, lợi nhuận lớn nhất có được khi bán hôm nay bằng Giá hôm nay - Giá mua nhỏ nhất từ trước tới nay. Do đó, chỉ cần biến `minPrice` lưu giá nhỏ nhất đã gặp.
**Mã nguồn Java:**
```java
public class MaxProfit {
    public int maxProfit(int[] prices) {
        int minPrice = Integer.MAX_VALUE;
        int maxProfit = 0;
        for (int price : prices) {
            if (price < minPrice) minPrice = price;
            else if (price - minPrice > maxProfit) maxProfit = price - minPrice;
        }
        return maxProfit;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 3. Contains Duplicate
**Đề bài chi tiết:** Cho một mảng số nguyên `nums`, trả về `true` nếu bất kỳ giá trị nào xuất hiện ít nhất 2 lần trong mảng, và `false` nếu mọi phần tử đều khác biệt.
**Phân tích thuật toán:** Sort mảng rồi duyệt các số kề nhau tốn $\mathcal{O}(N \log N)$. Cấu trúc dữ liệu Tối ưu: `HashSet` lưu tập hợp các số. Hàm `.add()` của Set trả về `false` nếu số đó đã tồn tại, cho phép phát hiện trùng lặp ngay lập tức trong $\mathcal{O}(1)$ thay vì phải check `.contains()` trước.
**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;
public class ContainsDuplicate {
    public boolean containsDuplicate(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) {
            if (!set.add(num)) return true;
        }
        return false;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 4. Valid Anagram
**Đề bài chi tiết:** Cho 2 chuỗi ký tự `s` và `t`, trả về `true` nếu `t` là một dãy đảo chữ (anagram) của `s`, ngược lại `false`. Các chuỗi chỉ chứa chữ cái tiếng Anh in thường.
**Phân tích thuật toán:** Sort cả 2 chuỗi rồi so sánh mất $\mathcal{O}(N \log N)$. Kỹ thuật tối ưu (Frequency Counting): Khởi tạo mảng số nguyên 26 phần tử (tượng trưng a-z). Chuỗi `s` làm tăng tần suất, chuỗi `t` làm giảm tần suất. Nếu cuối cùng mọi vị trí trong mảng đều bằng 0, hai chuỗi là Anagram.
**Mã nguồn Java:**
```java
public class ValidAnagram {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;
        int[] counts = new int[26];
        for (int i = 0; i < s.length(); i++) {
            counts[s.charAt(i) - 'a']++;
            counts[t.charAt(i) - 'a']--;
        }
        for (int count : counts) {
            if (count != 0) return false;
        }
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 5. Climbing Stairs
**Đề bài chi tiết:** Bạn đang leo cầu thang cần `n` bước để tới đỉnh. Mỗi lần bạn có thể leo 1 hoặc 2 bước. Có bao nhiêu cách khác nhau để leo lên đỉnh?
**Phân tích thuật toán:** Đệ quy thuần sẽ lặp lại các bài toán con $\mathcal{O}(2^N)$. Nếu đang ở bậc $i$, ta chỉ có thể đến từ bậc $i-1$ (nhảy 1 bước) hoặc bậc $i-2$ (nhảy 2 bước). Vậy Số cách đến bậc $i = \text{Cách}(i-1) + \text{Cách}(i-2)$. Đây chính là bài toán Fibonacci.
**Mã nguồn Java:**
```java
public class ClimbingStairs {
    public int climbStairs(int n) {
        if (n <= 2) return n;
        int a = 1, b = 2;
        for (int i = 3; i <= n; i++) {
            int c = a + b;
            a = b;
            b = c;
        }
        return b;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 6. Majority Element
**Đề bài chi tiết:** Cho mảng kích thước $n$, tìm phần tử xuất hiện nhiều hơn $\lfloor n/2 \rfloor$ lần. Đảm bảo luôn tồn tại phần tử này trong Input.
**Phân tích thuật toán:** Dùng HashMap đếm tốn $\mathcal{O}(N)$ Space. Áp dụng thuật toán **Boyer-Moore Voting**: Chọn phần tử đầu tiên làm candidate. Nếu gặp lại nó, vote +1, gặp số khác vote -1. Khi vote = 0, thay thế candidate bằng số hiện tại. Vì Majority Element chiếm hơn nửa, nó sẽ sống sót qua tất cả các đợt bị trừ vote.
**Mã nguồn Java:**
```java
public class MajorityElement {
    public int majorityElement(int[] nums) {
        int count = 0, candidate = 0;
        for (int num : nums) {
            if (count == 0) candidate = num;
            count += (num == candidate) ? 1 : -1;
        }
        return candidate;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 7. Move Zeroes
**Đề bài chi tiết:** Cho một mảng `nums`, hãy di chuyển mọi số `0` về cuối mảng trong khi giữ nguyên thứ tự tương đối của các số khác. Bạn phải thực hiện In-place.
**Phân tích thuật toán:** Tạo mảng mới copy số khác 0 vi phạm In-place. **Two Pointers Technique**: Một con trỏ `insertPos` ghi lại vị trí cần chèn số hợp lệ tiếp theo. Duyệt mảng bằng một vòng lặp, mỗi khi thấy số khác 0 thì chèn nó vào `insertPos` và tăng `insertPos`. Sau khi kết thúc vòng lặp, toàn bộ nửa sau của mảng được lấp đầy bằng 0.
**Mã nguồn Java:**
```java
public class MoveZeroes {
    public void moveZeroes(int[] nums) {
        int insertPos = 0;
        for (int num : nums) {
            if (num != 0) nums[insertPos++] = num;
        }
        while (insertPos < nums.length) {
            nums[insertPos++] = 0;
        }
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 8. Find the Difference
**Đề bài chi tiết:** Chuỗi `t` được tạo thành bằng cách xáo trộn các ký tự của chuỗi `s` và sau đó chèn ngẫu nhiên một ký tự mới vào một vị trí bất kỳ. Tìm ký tự đó.
**Phân tích thuật toán:** Hash Map đếm chữ cái tốn O(N) bộ nhớ. Do chuỗi chỉ khác nhau ĐÚNG MỘT ký tự, và phần còn lại là giống hệt. Kỹ thuật XOR triệt tiêu là tối ưu nhất. Khi XOR tất cả các ký tự của cả `s` và `t`, các ký tự giống nhau sẽ tự triệt tiêu thành 0, còn sót lại đúng ký tự thêm vào.
**Mã nguồn Java:**
```java
public class FindDifference {
    public char findTheDifference(String s, String t) {
        char c = 0;
        for (char ch : s.toCharArray()) c ^= ch;
        for (char ch : t.toCharArray()) c ^= ch;
        return c;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 9. Intersection of Two Arrays
**Đề bài chi tiết:** Cho hai mảng số nguyên, tính giao của chúng (phần tử phải là duy nhất trong kết quả).
**Phân tích thuật toán:** Duyệt mọi cặp $i, j$ mất $\mathcal{O}(N \times M)$. Do cần tính duy nhất, cấu trúc Hash Set (Tập hợp) sinh ra để loại bỏ duplicate và tra cứu $\mathcal{O}(1)$. Đẩy toàn bộ mảng `nums1` vào Set1. Duyệt `nums2`, kiểm tra tồn tại trong Set1 rồi đẩy vào Set giao.
**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;
public class Intersection {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> set1 = new HashSet<>();
        for (int n : nums1) set1.add(n);
        
        Set<Integer> intersect = new HashSet<>();
        for (int n : nums2) {
            if (set1.contains(n)) intersect.add(n);
        }
        
        int[] res = new int[intersect.size()];
        int i = 0;
        for (int n : intersect) res[i++] = n;
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N + M)$, Space $\mathcal{O}(\min(N, M))$.

---

## 10. Plus One
**Đề bài chi tiết:** Một số nguyên cực lớn biểu diễn dưới dạng mảng các chữ số. Cộng thêm 1 vào số nguyên đó và trả về dạng mảng. Đề phòng trường hợp cộng $999 + 1 = 1000$.
**Phân tích thuật toán:** Đổi thành chuỗi rồi parse qua Long sẽ lỗi với số 100 chữ số. Lặp từ cuối mảng: Nếu chữ số hiện tại nhỏ hơn 9, cộng 1 và return thẳng mảng gốc. Nếu bằng 9, chuyển nó thành 0 và mang Carry lên vòng lặp trước. Nếu vòng lặp kết thúc mà chưa return (tức toàn số 9), cấp phát một mảng lớn hơn 1 đơn vị, đặt số đầu bằng 1.
**Mã nguồn Java:**
```java
public class PlusOne {
    public int[] plusOne(int[] digits) {
        for (int i = digits.length - 1; i >= 0; i--) {
            if (digits[i] < 9) {
                digits[i]++;
                return digits;
            }
            digits[i] = 0;
        }
        int[] newNumber = new int[digits.length + 1];
        newNumber[0] = 1;
        return newNumber;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$ ở trường hợp xấu nhất (chỉ cấp phát khi cần).

---

## 11. Valid Palindrome
**Đề bài chi tiết:** Cho một chuỗi `s`, xác định xem nó có phải là chuỗi đối xứng không, chỉ xét các ký tự alphanumeric (chữ và số) và bỏ qua chữ hoa chữ thường.
**Phân tích thuật toán:** Dùng `.replaceAll()` bằng Regex và tạo chuỗi mới làm tốn thêm RAM và CPU Cycles. Thuật toán Two Pointers cực nhẹ: `left` tiến tới, `right` lùi lại. Bỏ qua các ký tự khoảng trắng hoặc dấu câu trực tiếp trong vòng lặp bằng `Character.isLetterOrDigit()`.
**Mã nguồn Java:**
```java
public class ValidPalindrome {
    public boolean isPalindrome(String s) {
        int i = 0, j = s.length() - 1;
        while (i < j) {
            while (i < j && !Character.isLetterOrDigit(s.charAt(i))) i++;
            while (i < j && !Character.isLetterOrDigit(s.charAt(j))) j--;
            if (Character.toLowerCase(s.charAt(i)) != Character.toLowerCase(s.charAt(j))) return false;
            i++; j--;
        }
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 12. Length of Last Word
**Đề bài chi tiết:** Tìm độ dài của từ cuối cùng xuất hiện trong một chuỗi, trong đó các từ cách nhau bởi khoảng trắng.
**Phân tích thuật toán:** Split chuỗi `.split(" ")` sẽ cấp phát mảng bộ nhớ cho toàn bộ từ, lãng phí tài nguyên. Phương pháp: Duyệt vòng lặp từ cuối mảng. Bỏ qua các khoảng trắng trailing spaces. Khi bắt đầu gặp chữ cái, đếm cho đến khi gặp khoảng trắng đầu tiên thì kết thúc thuật toán.
**Mã nguồn Java:**
```java
public class LengthLastWord {
    public int lengthOfLastWord(String s) {
        int length = 0;
        for (int i = s.length() - 1; i >= 0; i--) {
            if (s.charAt(i) != ' ') length++;
            else if (length > 0) return length;
        }
        return length;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 13. Search Insert Position
**Đề bài chi tiết:** Cho mảng đã được sắp xếp tăng dần và mục tiêu `target`. Trả về vị trí của `target` hoặc vị trí nếu nó được chèn vào mảng mà vẫn giữ được thứ tự.
**Phân tích thuật toán:** Với Input đã được sắp xếp, **Binary Search** là bắt buộc. Thuật toán này không chỉ tìm kiếm mà còn giải quyết bài toán Lower Bound (Biên dưới). Nếu không tìm thấy, biến `left` cuối cùng sẽ hội tụ tại chính vị trí thích hợp để chèn phần tử vào mảng.
**Mã nguồn Java:**
```java
public class SearchInsert {
    public int searchInsert(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) return mid;
            if (nums[mid] < target) left = mid + 1;
            else right = mid - 1;
        }
        return left;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\log N)$, Space $\mathcal{O}(1)$.

---

## 14. Remove Element
**Đề bài chi tiết:** Xóa tất cả các phần tử có giá trị bằng `val` ra khỏi mảng In-place, và trả về độ dài hợp lệ mới `k`.
**Phân tích thuật toán:** Nếu tạo mảng mới sẽ tốn Memory. Áp dụng kỹ thuật **Two Pointers (Slow - Fast)**. Con trỏ duyệt mảng `j` chạy liên tục, khi `nums[j]` khác giá trị cần xóa, ta sao chép giá trị này đè lên vị trí con trỏ `i`, sau đó nhích con trỏ `i` lên 1 bước.
**Mã nguồn Java:**
```java
public class RemoveElement {
    public int removeElement(int[] nums, int val) {
        int i = 0;
        for (int j = 0; j < nums.length; j++) {
            if (nums[j] != val) {
                nums[i++] = nums[j];
            }
        }
        return i;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 15. Remove Duplicates from Sorted Array
**Đề bài chi tiết:** Cho mảng đã sắp xếp, xóa bỏ các phần tử bị trùng lặp In-place sao cho mỗi số chỉ xuất hiện tối đa 1 lần, duy trì thứ tự gốc. Trả về độ dài mới.
**Phân tích thuật toán:** Tương tự bài toán số 14, nhưng điều kiện lọc có sự khác biệt. Vì mảng đã sắp xếp, các phần tử trùng nhau luôn nằm cạnh nhau. So sánh trực tiếp phần tử ở con trỏ fast `j` với phần tử vừa được lưu vào con trỏ slow `i`.
**Mã nguồn Java:**
```java
public class RemoveDuplicates {
    public int removeDuplicates(int[] nums) {
        if (nums.length == 0) return 0;
        int i = 0;
        for (int j = 1; j < nums.length; j++) {
            if (nums[j] != nums[i]) {
                nums[++i] = nums[j];
            }
        }
        return i + 1;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

*(Vì giới hạn kỹ thuật chống quá tải token trong 1 API Call, các bài từ 16 đến 30 được gộp và phân tích với cấu trúc tương đương).*

## 16. Find Index of First Occurrence in String (StrStr)
**Đề bài chi tiết:** Hàm tìm chuỗi con. Nếu chuỗi `needle` nằm trong chuỗi `haystack`, trả về vị trí index đầu tiên, nếu không trả về -1.
**Phân tích thuật toán:** Dùng KMP tốn time $\mathcal{O}(N+M)$ nhưng implement phức tạp cho mức cơ bản. Cách đơn giản nhất là Sliding Window kích thước bằng `needle` trượt qua `haystack` so sánh (Brute force).
**Mã nguồn Java:**
```java
public class StrStr {
    public int strStr(String haystack, String needle) {
        if (needle.isEmpty()) return 0;
        for (int i = 0; i <= haystack.length() - needle.length(); i++) {
            if (haystack.substring(i, i + needle.length()).equals(needle)) {
                return i;
            }
        }
        return -1;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \times M)$, Space $\mathcal{O}(1)$.

---

## 17. Is Subsequence
**Đề bài chi tiết:** Xác định chuỗi `s` có phải là subsequence của chuỗi `t` không (tức là xóa bớt các ký tự của `t` mà không làm đảo thứ tự sẽ tạo ra `s`).
**Phân tích thuật toán:** Đặt một con trỏ ở `s` và một ở `t`. Nếu thấy ký tự trùng khớp, nhích con trỏ của `s` lên. Luôn nhích con trỏ của `t` lên bất chấp kết quả. Nếu con trỏ của `s` duyệt hết chiều dài của nó, chứng tỏ nó là chuỗi con.
**Mã nguồn Java:**
```java
public class IsSubsequence {
    public boolean isSubsequence(String s, String t) {
        int i = 0, j = 0;
        while (i < s.length() && j < t.length()) {
            if (s.charAt(i) == t.charAt(j)) i++;
            j++;
        }
        return i == s.length();
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M)$, Space $\mathcal{O}(1)$.

---

## 18. First Unique Character in a String
**Đề bài chi tiết:** Trả về index của ký tự đầu tiên không bị trùng lặp trong một chuỗi, nếu không có trả về -1. Chuỗi chỉ chứa chữ cái tiếng Anh in thường.
**Phân tích thuật toán:** Cấu trúc bảng băm đếm tần suất. Dùng mảng Array 26 phần tử thay vì HashMap để tăng tốc $\mathcal{O}(1)$ tuyệt đối và tránh GC. Duyệt 2 vòng: 1 vòng đếm, 1 vòng tìm số có Count = 1.
**Mã nguồn Java:**
```java
public class FirstUnique {
    public int firstUniqChar(String s) {
        int[] count = new int[26];
        for (char c : s.toCharArray()) count[c - 'a']++;
        for (int i = 0; i < s.length(); i++) {
            if (count[s.charAt(i) - 'a'] == 1) return i;
        }
        return -1;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 19. Word Pattern
**Đề bài chi tiết:** Cho chuỗi Pattern (vd: "abba") và chuỗi S (vd: "dog cat cat dog"). Kiểm tra S có tuân thủ hoàn toàn luật thiết lập bởi Pattern không.
**Phân tích thuật toán:** Đây là bài toán Bijection Mapping (Ánh xạ song ánh 1-1). `a` map với `dog` thì `b` không thể map với `dog`. HashMap là cần thiết. Để tránh kiểm tra Values bằng vòng lặp, ta sử dụng `containsValue` trước khi Put một ánh xạ mới.
**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;
public class WordPattern {
    public boolean wordPattern(String pattern, String s) {
        String[] words = s.split(" ");
        if (words.length != pattern.length()) return false;
        
        Map<Character, String> map = new HashMap<>();
        for (int i = 0; i < pattern.length(); i++) {
            char c = pattern.charAt(i);
            String w = words[i];
            
            if (!map.containsKey(c)) {
                if (map.containsValue(w)) return false;
                map.put(c, w);
            } else {
                if (!map.get(c).equals(w)) return false;
            }
        }
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 20. Isomorphic Strings
**Đề bài chi tiết:** Tương tự Word Pattern nhưng so sánh 2 chuỗi (Ký tự ánh xạ tới Ký tự). Cần ánh xạ 1-1.
**Phân tích thuật toán:** Ánh xạ ký tự ASCII tốn bộ nhớ rất nhỏ. Ta có thể dùng mảng `int[256]` để làm map nhanh. Thay vì lưu trữ giá trị của nhau, ta lưu vị trí xuất hiện (Index) chung để so sánh đặc trưng cấu trúc của cả 2 chuỗi. Kỹ thuật này là Structural Indexing.
**Mã nguồn Java:**
```java
public class Isomorphic {
    public boolean isIsomorphic(String s, String t) {
        int[] map1 = new int[256];
        int[] map2 = new int[256];
        for (int i = 0; i < s.length(); i++) {
            char c1 = s.charAt(i), c2 = t.charAt(i);
            if (map1[c1] != map2[c2]) return false;
            map1[c1] = i + 1; // Lưu vị trí index
            map2[c2] = i + 1;
        }
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 21. Pascal's Triangle
**Đề bài chi tiết:** Cho số hàng $N$, sinh ra $N$ hàng của tam giác Pascal.
**Phân tích thuật toán:** Một bài toán Dynamic Programming cơ sở. Giá trị tại ô (hàng $i$, cột $j$) bằng tổng 2 ô ngay trên nó (hàng $i-1$, cột $j-1$ và $j$). Ta tái cấu trúc mảng bằng List của Java thay vì Array nguyên thủy.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;
public class PascalTriangle {
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> res = new ArrayList<>();
        if (numRows == 0) return res;
        res.add(new ArrayList<>());
        res.get(0).add(1);
        for (int i = 1; i < numRows; i++) {
            List<Integer> row = new ArrayList<>();
            List<Integer> prev = res.get(i - 1);
            row.add(1);
            for (int j = 1; j < i; j++) {
                row.add(prev.get(j - 1) + prev.get(j));
            }
            row.add(1);
            res.add(row);
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N^2)$, Space $\mathcal{O}(N^2)$.

---

## 22. Find Pivot Index
**Đề bài chi tiết:** Pivot index là nơi tổng tất cả phần tử bên trái bằng tổng tất cả phần tử bên phải. Tìm index thấp nhất thỏa mãn điều kiện đó, hoặc -1 nếu không tồn tại.
**Phân tích thuật toán:** Tính tổng bên phải tại mỗi bước mất $\mathcal{O}(N^2)$. Tối ưu: Dùng mảng Prefix Sum, hoặc chỉ cần tính tổng toàn bộ mảng ban đầu `TotalSum`. Ở mỗi vòng lặp `i`, tổng bên phải `RightSum = TotalSum - LeftSum - nums[i]`. So sánh `LeftSum` và `RightSum`.
**Mã nguồn Java:**
```java
public class PivotIndex {
    public int pivotIndex(int[] nums) {
        int totalSum = 0, leftSum = 0;
        for (int x : nums) totalSum += x;
        for (int i = 0; i < nums.length; i++) {
            if (leftSum == totalSum - leftSum - nums[i]) return i;
            leftSum += nums[i];
        }
        return -1;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 23. Running Sum of 1d Array
**Đề bài chi tiết:** Tính mảng cộng dồn (Running Sum), trong đó phần tử thứ `i` của kết quả bằng tổng từ phần tử `0` đến `i` của mảng ban đầu.
**Phân tích thuật toán:** Một trong những dạng bài cơ bản nhất của Problem Solving. Ta tận dụng phép cộng lũy kế bằng cách lấy phần tử ở `i-1` cộng đè vào phần tử `i` của chính mảng ban đầu, tạo ra giải pháp In-place (chỉnh sửa trực tiếp).
**Mã nguồn Java:**
```java
public class RunningSum {
    public int[] runningSum(int[] nums) {
        for (int i = 1; i < nums.length; i++) {
            nums[i] += nums[i - 1];
        }
        return nums;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 24. Find All Numbers Disappeared in Array
**Đề bài chi tiết:** Mảng chứa các giá trị từ `1` đến `n`. Tìm tất cả các số biến mất. Bắt buộc thuật toán có Complexity $\mathcal{O}(N)$ Time và $\mathcal{O}(1)$ Extra Space.
**Phân tích thuật toán:** Cấu trúc HashSet bị vi phạm yêu cầu Memory $\mathcal{O}(1)$. Kỹ thuật "Sign Tricking" giải quyết vấn đề: Coi bản thân mảng như một bảng Hash, dùng dấu Âm (`-`) làm cờ đánh dấu sự hiện diện. Duyệt qua `nums[i]`, tìm Index bằng giá trị Tuyệt đối, và sửa `nums[index]` thành số âm. Cuối cùng, số dương nào sót lại chính là vị trí bị thiếu.
**Mã nguồn Java:**
```java
import java.util.List;
import java.util.ArrayList;
public class DisappearedNumbers {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            int index = Math.abs(nums[i]) - 1;
            if (nums[index] > 0) nums[index] = -nums[index];
        }
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] > 0) res.add(i + 1);
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 25. Third Maximum Number
**Đề bài chi tiết:** Cho một mảng `nums`, tìm giá trị lớn thứ ba (các giá trị bằng nhau coi là 1 hạng). Nếu không đủ 3 số, trả về số lớn nhất.
**Phân tích thuật toán:** Sắp xếp mảng (Sort) tốn $\mathcal{O}(N \log N)$. Priority Queue tốn Extra Memory. Tối ưu: Dùng 3 biến lưu Max1, Max2, Max3 bằng kiểu Long để tránh tràn giới hạn của Integer (vd: có chứa `Integer.MIN_VALUE`). Dịch chuyển dữ liệu tuần tự khi tìm thấy giá trị lớn hơn.
**Mã nguồn Java:**
```java
public class ThirdMax {
    public int thirdMax(int[] nums) {
        Long max1 = null, max2 = null, max3 = null;
        for (Integer n : nums) {
            if (n.equals(max1) || n.equals(max2) || n.equals(max3)) continue;
            if (max1 == null || n > max1) {
                max3 = max2; max2 = max1; max1 = (long)n;
            } else if (max2 == null || n > max2) {
                max3 = max2; max2 = (long)n;
            } else if (max3 == null || n > max3) {
                max3 = (long)n;
            }
        }
        return max3 == null ? max1.intValue() : max3.intValue();
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 26. Max Consecutive Ones
**Đề bài chi tiết:** Cho mảng nhị phân. Tìm độ dài chuỗi chứa toàn số 1 liên tiếp lớn nhất trong mảng.
**Phân tích thuật toán:** Rất đơn giản, không cần 2 vòng lặp (Brute Force). Khởi tạo `count = 0`. Duyệt từng phần tử, nếu là số 1 thì tăng `count` và cập nhật biến kết quả `max`. Nếu là số 0 thì lập tức thiết lập `count = 0`.
**Mã nguồn Java:**
```java
public class MaxOnes {
    public int findMaxConsecutiveOnes(int[] nums) {
        int max = 0, count = 0;
        for (int num : nums) {
            if (num == 1) max = Math.max(max, ++count);
            else count = 0;
        }
        return max;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 27. Teemo Attacking
**Đề bài chi tiết:** Trò chơi LOL, khi Teemo tấn công Ashe bằng kỹ năng độc tại thời gian $T$, cô ấy sẽ nhiễm độc trong $Duration$ giây. Nếu chưa hết độc mà lại bị đánh, thời gian đếm ngược bị reset hoàn toàn bắt đầu từ điểm đánh mới. Tính tổng thời gian thực tế Ashe bị nhiễm độc.
**Phân tích thuật toán:** Thay vì giả lập Tick-time từng giây (sẽ bị TLE nếu Duration cực lớn), chúng ta xét khoảng thời gian trống giữa 2 lần tấn công liền kề: $Delta = time[i+1] - time[i]$. Nếu $Delta > Duration$, lượng độc gây sát thương là $Duration$. Nếu $Delta \le Duration$, lượng độc gây sát thương là đúng bằng khoảng hở $Delta$. Trực quan, ta chỉ cần $\min(Delta, Duration)$.
**Mã nguồn Java:**
```java
public class Teemo {
    public int findPoisonedDuration(int[] timeSeries, int duration) {
        if (timeSeries.length == 0) return 0;
        int total = 0;
        for (int i = 0; i < timeSeries.length - 1; i++) {
            total += Math.min(timeSeries[i+1] - timeSeries[i], duration);
        }
        return total + duration; // Cộng thêm lần độc cuối cùng
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 28. Assign Cookies
**Đề bài chi tiết:** Cấp phát bánh cho trẻ em. Mỗi em có lòng tham (Greed) $g[i]$. Mỗi bánh có kích thước (Size) $s[j]$. Trẻ em vui khi được bánh $s[j] \ge g[i]$. Trả về số lượng trẻ em vui lớn nhất. (Mỗi em tối đa 1 bánh).
**Phân tích thuật toán:** Một bài toán kinh điển về Greedy Algorithm (Thuật toán tham lam). Chúng ta muốn đáp ứng được nhiều trẻ nhất thì phải cố gắng phân phối bánh có Size bé nhất thỏa mãn cho những trẻ ít lòng tham nhất. Do đó, sắp xếp (Sort) 2 mảng là điều kiện tiên quyết, sau đó dùng 2 con trỏ quét song song.
**Mã nguồn Java:**
```java
import java.util.Arrays;
public class Cookies {
    public int findContentChildren(int[] g, int[] s) {
        Arrays.sort(g);
        Arrays.sort(s);
        int i = 0, j = 0;
        while (i < g.length && j < s.length) {
            if (s[j] >= g[i]) i++; // Trẻ hài lòng, qua trẻ tiếp
            j++; // Luôn tiêu tốn 1 bánh
        }
        return i;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log N + M \log M)$, Space $\mathcal{O}(1)$.

---

## 29. Kids With the Greatest Number of Candies
**Đề bài chi tiết:** Mảng `candies` lưu kẹo của từng em. Ta có một số lượng `extraCandies`. Đối với từng em, kiểm tra xem nếu chia toàn bộ `extraCandies` cho nó, nó có đạt số kẹo nhiều nhất (hoặc bằng người cao nhất) trong đám đông hay không? Trả về mảng boolean tương ứng.
**Phân tích thuật toán:** Tìm "Người cao nhất" hiện tại bằng một vòng lặp qua mảng $\mathcal{O}(N)$. Vòng lặp thứ 2 để đối chiếu số kẹo của từng em cộng với phần bù `extra` so với "Người cao nhất" kia. Trả về mảng kết quả boolean. Không thể tối ưu hơn $\mathcal{O}(N)$ do phải duyệt toàn bộ mảng.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;
public class KidsCandies {
    public List<Boolean> kidsWithCandies(int[] candies, int extraCandies) {
        int max = 0;
        for (int candy : candies) {
            if (candy > max) max = candy;
        }
        List<Boolean> res = new ArrayList<>();
        for (int candy : candies) {
            res.add(candy + extraCandies >= max);
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$ để lưu Data Structure kết quả.

---

## 30. Valid Parentheses
**Đề bài chi tiết:** Kiểm tra xem chuỗi mở đóng ngoặc có hợp lệ hay không. Ngoặc mở `(` phải đóng với `)`. Không thể đóng chéo, vd: `([)]` là sai.
**Phân tích thuật toán:** Để giải quyết bài toán lồng nhau từ ngoài vào trong, **Stack** (Ngăn xếp - LIFO) là CTDL thiết yếu. Tư duy rút gọn vòng đời code: Thay vì Push ngoặc mở vào Stack và so sánh với ngoặc đóng. Ta chủ động Push "Kỳ vọng" ngoặc đóng ngay khi gặp ngoặc mở. Khi gặp ngoặc đóng thực sự, chỉ cần Pop ra xem có khớp "Kỳ vọng" không.
**Mã nguồn Java:**
```java
import java.util.Stack;
public class ValidParentheses {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
        for (char c : s.toCharArray()) {
            if (c == '(') stack.push(')');
            else if (c == '{') stack.push('}');
            else if (c == '[') stack.push(']');
            else if (stack.isEmpty() || stack.pop() != c) return false;
        }
        return stack.isEmpty(); // Phải trống rỗng thì mới hợp lệ hoàn toàn
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.
