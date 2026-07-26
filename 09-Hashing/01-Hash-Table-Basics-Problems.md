# Hash Table Basics Problems

Tài liệu này bao gồm 30 bài tập cơ bản về Hash Table. 10 bài đầu tiên sẽ có lời giải chi tiết (Phân tích, Java Code, Độ phức tạp). Các bài còn lại được tóm tắt.

---

## 1. Two Sum

**Đề bài chi tiết:**
Cho một mảng số nguyên `nums` và một số nguyên `target`. Hãy trả về chỉ số (indices) của hai số trong mảng sao cho tổng của chúng bằng `target`.
Giả định mỗi đầu vào chỉ có chính xác một kết quả, và bạn không được sử dụng cùng một phần tử hai lần.

**Phân tích thuật toán:**
Dùng một HashMap để lưu trữ giá trị của từng phần tử cùng với chỉ số của nó `(value, index)`. Khi duyệt qua mảng tại phần tử `x`, ta tính phần dư cần tìm là `complement = target - x`. Nếu `complement` đã tồn tại trong HashMap, ta đã tìm thấy cặp phần tử.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (map.containsKey(complement)) {
                return new int[] { map.get(complement), i };
            }
            map.put(nums[i], i);
        }
        return new int[]{};
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N)$, vì chỉ cần duyệt qua mảng 1 lần.
- Không gian (Space Complexity): $O(N)$, lưu trữ nhiều nhất $N$ phần tử trong Hash Map.

---

## 2. Contains Duplicate

**Đề bài chi tiết:**
Cho một mảng số nguyên `nums`. Trả về `true` nếu bất kỳ giá trị nào xuất hiện ít nhất hai lần trong mảng, ngược lại trả về `false`.

**Phân tích thuật toán:**
Dùng HashSet để lưu các số đã gặp. Nếu duyệt tới phần tử nào mà đã tồn tại trong Set, tức là có phần tử lặp lại, lập tức trả về `true`.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public boolean containsDuplicate(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) {
            if (set.contains(num)) {
                return true;
            }
            set.add(num);
        }
        return false;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): $O(N)$.
- Không gian (Space): $O(N)$.

---

## 3. Valid Anagram

**Đề bài chi tiết:**
Cho hai chuỗi `s` và `t`, trả về `true` nếu `t` là một Anagram của `s`, ngược lại trả về `false`. (Anagram là từ hoặc cụm từ hình thành bằng cách sắp xếp lại các chữ cái của từ khác).

**Phân tích thuật toán:**
Do các ký tự thường chỉ là lowercase English letters, ta có thể dùng một mảng đếm tần suất kích thước 26 (chính là một mảng băm). Hoặc dùng HashMap chung. Cộng tần suất các ký tự của `s` và trừ tần suất khi duyệt `t`. Cuối cùng kiểm tra xem tất cả các tần suất có bằng 0 không.

**Mã nguồn Java:**
```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;
        
        int[] count = new int[26];
        for (int i = 0; i < s.length(); i++) {
            count[s.charAt(i) - 'a']++;
            count[t.charAt(i) - 'a']--;
        }
        
        for (int c : count) {
            if (c != 0) return false;
        }
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): $O(N)$.
- Không gian (Space): $O(1)$ do mảng chỉ có 26 phần tử (hằng số).

---

## 4. Intersection of Two Arrays II

**Đề bài chi tiết:**
Cho hai mảng số nguyên `nums1` và `nums2`, hãy trả về giao của hai mảng (những phần tử xuất hiện trong cả 2 mảng tính cả số lượng lặp lại).

**Phân tích thuật toán:**
Dùng một HashMap để đếm số lần xuất hiện của các phần tử trong `nums1`. Sau đó duyệt qua `nums2`, nếu phần tử tồn tại trong map và có số đếm > 0, đưa vào mảng kết quả và giảm số đếm đi 1.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.List;
import java.util.ArrayList;

class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        HashMap<Integer, Integer> map = new HashMap<>();
        for (int num : nums1) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }
        
        List<Integer> list = new ArrayList<>();
        for (int num : nums2) {
            if (map.containsKey(num) && map.get(num) > 0) {
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
- Thời gian (Time): $O(M + N)$.
- Không gian (Space): $O(\min(M, N))$ cho kích thước Map.

---

## 5. First Unique Character in a String

**Đề bài chi tiết:**
Cho một chuỗi `s`, tìm ký tự không bị lặp lại đầu tiên và trả về chỉ số của nó. Nếu không tồn tại, trả về -1.

**Phân tích thuật toán:**
Duyệt chuỗi lần thứ nhất, đếm tần suất xuất hiện của các ký tự và lưu vào HashMap hoặc mảng 26 phần tử. Duyệt lại chuỗi lần thứ 2, kiểm tra xem ký tự tại index đang xét có tần suất là 1 hay không.

**Mã nguồn Java:**
```java
class Solution {
    public int firstUniqChar(String s) {
        int[] freq = new int[26];
        
        for (int i = 0; i < s.length(); i++) {
            freq[s.charAt(i) - 'a']++;
        }
        
        for (int i = 0; i < s.length(); i++) {
            if (freq[s.charAt(i) - 'a'] == 1) {
                return i;
            }
        }
        return -1;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): $O(N)$ duyệt chuỗi 2 lần.
- Không gian (Space): $O(1)$ chỉ lưu đúng 26 ký tự.

---

## 6. Find the Difference

**Đề bài chi tiết:**
Chuỗi `t` được tạo ra bởi việc xáo trộn ngẫu nhiên chuỗi `s` và thêm vào một ký tự ngẫu nhiên ở một vị trí bất kỳ. Tìm ký tự được thêm vào `t`.

**Phân tích thuật toán:**
Có thể dùng HashMap đếm ký tự. Tuy nhiên, một cách dùng tổng giá trị ASCII (như Hash Code nguyên thủy) hoặc toán tử XOR siêu hiệu quả do chỉ dư 1 ký tự. Ở đây dùng mảng đếm tần suất 26 (Hash Table đơn giản).

**Mã nguồn Java:**
```java
class Solution {
    public char findTheDifference(String s, String t) {
        int[] count = new int[26];
        for (char c : s.toCharArray()) count[c - 'a']++;
        for (char c : t.toCharArray()) {
            count[c - 'a']--;
            if (count[c - 'a'] < 0) return c;
        }
        return ' ';
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): $O(N)$.
- Không gian (Space): $O(1)$.

---

## 7. Single Number

**Đề bài chi tiết:**
Cho một mảng `nums`, mỗi phần tử xuất hiện hai lần ngoại trừ một phần tử xuất hiện duy nhất 1 lần. Tìm phần tử đơn độc đó. (Yêu cầu $O(N)$ Time và $O(1)$ Space, nhưng sẽ hướng dẫn dùng Hash Table để học khái niệm).

**Phân tích thuật toán:**
Dùng HashSet, khi gặp phần tử đã tồn tại, ta xóa phần tử đó đi. Cuối cùng HashSet chỉ còn duy nhất phần tử xuất hiện 1 lần. (Ghi chú: dùng toán tử XOR sẽ đạt được $O(1)$ bộ nhớ).

**Mã nguồn Java:**
```java
import java.util.HashSet;

class Solution {
    public int singleNumber(int[] nums) {
        HashSet<Integer> set = new HashSet<>();
        for (int num : nums) {
            if (set.contains(num)) {
                set.remove(num);
            } else {
                set.add(num);
            }
        }
        return set.iterator().next();
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): $O(N)$.
- Không gian (Space): $O(N)$ bằng kích thước của HashSet nửa số lượng phần tử.

---

## 8. Missing Number

**Đề bài chi tiết:**
Cho mảng `nums` chứa $N$ phần tử khác biệt nhau thuộc khoảng $[0, N]$. Tìm số duy nhất trong đoạn $[0, N]$ bị thiếu trong mảng.

**Phân tích thuật toán:**
Dùng HashSet lưu toàn bộ mảng. Sau đó cho một vòng lặp từ $0$ đến $N$, kiểm tra số nào không tồn tại trong HashSet thì đó là kết quả. (Ghi chú: thuật toán tối ưu là dùng Toán tính tổng $n*(n+1)/2$).

**Mã nguồn Java:**
```java
import java.util.HashSet;

class Solution {
    public int missingNumber(int[] nums) {
        HashSet<Integer> set = new HashSet<>();
        for (int num : nums) {
            set.add(num);
        }
        
        for (int i = 0; i <= nums.length; i++) {
            if (!set.contains(i)) {
                return i;
            }
        }
        return -1;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): $O(N)$.
- Không gian (Space): $O(N)$.

---

## 9. Maximum Number of Balloons

**Đề bài chi tiết:**
Cho chuỗi văn bản `text`, hãy sử dụng các ký tự của chuỗi để tạo ra nhiều từ "balloon" nhất có thể. Mỗi ký tự trong `text` chỉ có thể dùng tối đa 1 lần. Trả về số lượng lớn nhất các từ "balloon" có thể tạo ra.

**Phân tích thuật toán:**
Dùng Hash Table (mảng 26 ký tự) để đếm các ký tự `b, a, l, o, n`. 
Từ "balloon" cần: 1 'b', 1 'a', 2 'l', 2 'o', 1 'n'. 
Số lượng từ tạo được là giá trị tối thiểu của các tỷ lệ trên.

**Mã nguồn Java:**
```java
class Solution {
    public int maxNumberOfBalloons(String text) {
        int[] count = new int[26];
        for (char c : text.toCharArray()) {
            count[c - 'a']++;
        }
        
        int b = count['b' - 'a'];
        int a = count['a' - 'a'];
        int l = count['l' - 'a'] / 2;
        int o = count['o' - 'a'] / 2;
        int n = count['n' - 'a'];
        
        return Math.min(Math.min(b, a), Math.min(Math.min(l, o), n));
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): $O(N)$.
- Không gian (Space): $O(1)$.

---

## 10. Valid Sudoku

**Đề bài chi tiết:**
Xác định một bảng Sudoku $9 \times 9$ có hợp lệ không. Chỉ tính các ô đã điền. Hợp lệ nếu: 
1. Mỗi hàng chứa số 1-9 không lặp lại.
2. Mỗi cột chứa số 1-9 không lặp lại.
3. Mỗi lưới con $3 \times 3$ chứa số 1-9 không lặp lại.

**Phân tích thuật toán:**
Sử dụng HashSet để lưu trạng thái "số, vị trí". Ví dụ tạo chuỗi: `hàng i có số x`, `cột j có số x`, `block k có số x`. Nếu `add()` vào HashSet trả về false nghĩa là trạng thái đó đã tồn tại (lặp lại), bảng không hợp lệ.

**Mã nguồn Java:**
```java
import java.util.HashSet;

class Solution {
    public boolean isValidSudoku(char[][] board) {
        HashSet<String> seen = new HashSet<>();
        for (int i = 0; i < 9; ++i) {
            for (int j = 0; j < 9; ++j) {
                char number = board[i][j];
                if (number != '.') {
                    if (!seen.add(number + " in row " + i) ||
                        !seen.add(number + " in col " + j) ||
                        !seen.add(number + " in block " + i/3 + "-" + j/3))
                        return false;
                }
            }
        }
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): $O(1)$ do kích thước bảng luôn là $9 \times 9$.
- Không gian (Space): $O(1)$ tối đa lưu 81*3 phần tử trong HashSet.

---

## 11. Isomorphic Strings

**Đề bài chi tiết:**
Cho hai chuỗi `s` và `t`, xác định xem chúng có đẳng cấu (isomorphic) hay không.
Hai chuỗi `s` và `t` được gọi là đẳng cấu nếu các ký tự trong `s` có thể được thay thế để tạo thành `t`.
Mỗi ký tự phải ánh xạ tới một ký tự khác trong khi vẫn giữ nguyên thứ tự. Không có hai ký tự nào có thể ánh xạ đến cùng một ký tự, nhưng một ký tự có thể ánh xạ đến chính nó.

**Phân tích thuật toán:**
Dùng hai mảng (hoặc hai HashMap) để lưu lại vị trí xuất hiện cuối cùng của mỗi ký tự trong `s` và `t`. Khi duyệt qua cả hai chuỗi, nếu vị trí lưu trữ gần nhất của ký tự `s[i]` và `t[i]` khác nhau, có nghĩa là chúng không tuân theo quy luật ánh xạ 1-1, ta trả về `false`. Nếu bằng nhau, ta cập nhật lại vị trí mới cho cả hai ký tự.

**Mã nguồn Java:**
```java
class Solution {
    public boolean isIsomorphic(String s, String t) {
        if (s.length() != t.length()) return false;
        int[] map1 = new int[256];
        int[] map2 = new int[256];
        
        for (int i = 0; i < s.length(); i++) {
            char c1 = s.charAt(i);
            char c2 = t.charAt(i);
            if (map1[c1] != map2[c2]) {
                return false;
            }
            // Cập nhật vị trí xuất hiện (i + 1 để tránh nhầm lẫn với giá trị mặc định 0)
            map1[c1] = i + 1;
            map2[c2] = i + 1;
        }
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N)$ với $N$ là chiều dài của chuỗi.
- Không gian (Space Complexity): $O(1)$ vì bảng mã ASCII chỉ có 256 ký tự (hằng số).

---

## 12. Word Pattern

**Đề bài chi tiết:**
Cho một chuỗi mẫu `pattern` và một chuỗi `s`, xác định xem `s` có tuân theo đúng khuôn mẫu `pattern` hay không.
Việc tuân theo ở đây có nghĩa là tồn tại một song ánh (bijection) giữa một chữ cái trong `pattern` và một từ không trống trong `s`.

**Phân tích thuật toán:**
Chia chuỗi `s` thành mảng các từ (words) phân tách bằng khoảng trắng. Dùng một HashMap để ánh xạ từ ký tự của `pattern` sang từ tương ứng trong `s`. Đồng thời dùng một HashSet (hoặc hàm `containsValue`) để đảm bảo không có hai ký tự nào trong `pattern` cùng ánh xạ đến một từ. Nếu độ dài không khớp hoặc ánh xạ bị sai lệch, trả về `false`.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public boolean wordPattern(String pattern, String s) {
        String[] words = s.split(" ");
        if (words.length != pattern.length()) return false;
        
        Map<Character, String> map = new HashMap<>();
        
        for (int i = 0; i < pattern.length(); i++) {
            char c = pattern.charAt(i);
            String word = words[i];
            
            if (map.containsKey(c)) {
                if (!map.get(c).equals(word)) return false;
            } else {
                if (map.containsValue(word)) return false;
                map.put(c, word);
            }
        }
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N)$, với $N$ là số lượng từ trong `s` (hoặc chiều dài `s`).
- Không gian (Space Complexity): $O(N)$ cho mảng các từ `words` và bảng băm.

---

## 13. Happy Number

**Đề bài chi tiết:**
Viết một thuật toán để xác định xem một số `n` có phải là "số hạnh phúc" (happy number) hay không.
Số hạnh phúc là một số được định nghĩa theo quy trình sau:
- Bắt đầu với một số nguyên dương bất kỳ, thay thế số đó bằng tổng bình phương các chữ số của nó.
- Lặp lại quy trình cho đến khi số đó bằng 1 (lúc đó nó sẽ dừng lại), hoặc nó lặp vô tận trong một chu kỳ không bao gồm số 1.
- Những số mà quy trình này kết thúc ở 1 là những số hạnh phúc.

**Phân tích thuật toán:**
Quá trình tính tổng bình phương các chữ số sẽ dẫn đến hai kết quả: bằng 1 (số hạnh phúc), hoặc rơi vào một chu trình lặp vô hạn. Ta sử dụng HashSet để lưu lại những số đã từng xuất hiện. Nếu tính ra một số mà số đó đã có trong HashSet, có nghĩa là đã rơi vào chu trình vô hạn, ta dừng lại và trả về `false`.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    private int getNext(int n) {
        int totalSum = 0;
        while (n > 0) {
            int d = n % 10;
            n = n / 10;
            totalSum += d * d;
        }
        return totalSum;
    }

    public boolean isHappy(int n) {
        Set<Integer> seen = new HashSet<>();
        while (n != 1 && !seen.contains(n)) {
            seen.add(n);
            n = getNext(n);
        }
        return n == 1;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(\log N)$, vòng lặp phụ thuộc vào số chữ số của $N$.
- Không gian (Space Complexity): $O(\log N)$, do sử dụng HashSet để lưu lịch sử các số sinh ra.

---

## 14. Longest Palindrome

**Đề bài chi tiết:**
Cho một chuỗi `s` chỉ bao gồm các chữ cái in hoa và in thường, hãy tìm độ dài của chuỗi Palindrome dài nhất có thể được xây dựng bằng những chữ cái đó. Chữ cái phân biệt hoa thường (ví dụ: "Aa" không phải là palindrome).

**Phân tích thuật toán:**
Một chuỗi Palindrome có các ký tự (trừ ký tự ở giữa nếu chuỗi lẻ) xuất hiện số lần chẵn. Dùng HashSet để kiểm tra cặp ký tự. Nếu một ký tự xuất hiện lần 2, ta tăng độ dài palindrome lên 2 và xóa nó khỏi tập hợp. Nếu kết thúc duyệt mà tập hợp vẫn còn ký tự, ta có thể đặt 1 ký tự vào giữa chuỗi Palindrome, nên cộng thêm 1 vào kết quả.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public int longestPalindrome(String s) {
        Set<Character> set = new HashSet<>();
        int length = 0;
        
        for (char c : s.toCharArray()) {
            if (set.contains(c)) {
                length += 2;
                set.remove(c);
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
- Thời gian (Time Complexity): $O(N)$ với $N$ là chiều dài chuỗi.
- Không gian (Space Complexity): $O(1)$ vì bảng mã ASCII tối đa 128/256 ký tự.

---

## 15. Keyboard Row

**Đề bài chi tiết:**
Cho một mảng các chuỗi `words`, hãy trả về danh sách các từ (words) mà các chữ cái của nó chỉ có thể gõ bằng cách sử dụng các phím trên cùng một hàng của bàn phím máy tính theo chuẩn QWERTY.
- Hàng 1: "qwertyuiop"
- Hàng 2: "asdfghjkl"
- Hàng 3: "zxcvbnm"

**Phân tích thuật toán:**
Lưu chỉ mục hàng của từng ký tự trong mảng 26 ký tự từ `a` đến `z`. Duyệt qua từng từ trong `words`, kiểm tra xem toàn bộ các ký tự của từ đó (sau khi chuyển thành in thường) có cùng một số chỉ mục hàng hay không. Nếu có, đưa từ đó vào mảng kết quả.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public String[] findWords(String[] words) {
        String[] rows = {"qwertyuiop", "asdfghjkl", "zxcvbnm"};
        int[] rowMap = new int[26];
        
        for (int i = 0; i < rows.length; i++) {
            for (char c : rows[i].toCharArray()) {
                rowMap[c - 'a'] = i;
            }
        }
        
        List<String> result = new ArrayList<>();
        for (String word : words) {
            String lower = word.toLowerCase();
            boolean sameRow = true;
            int firstRow = rowMap[lower.charAt(0) - 'a'];
            
            for (char c : lower.toCharArray()) {
                if (rowMap[c - 'a'] != firstRow) {
                    sameRow = false;
                    break;
                }
            }
            if (sameRow) {
                result.add(word);
            }
        }
        
        return result.toArray(new String[0]);
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N)$ với $N$ là tổng số lượng ký tự trong mảng `words`.
- Không gian (Space Complexity): $O(1)$ cho mảng ánh xạ 26 ký tự.

---

## 16. Distribute Candies

**Đề bài chi tiết:**
Alice có $N$ viên kẹo, trong đó viên thứ $i$ có loại là `candyType[i]`. Alice chỉ có thể ăn tối đa $N / 2$ viên kẹo. Hãy tìm số lượng loại kẹo tối đa mà Alice có thể ăn.

**Phân tích thuật toán:**
Số lượng kẹo tối đa Alice được ăn là $N / 2$. Số lượng loại kẹo tối đa có sẵn là số lượng các loại kẹo duy nhất (tìm bằng HashSet). Kết quả sẽ là giá trị nhỏ hơn giữa số lượng loại kẹo duy nhất và $N / 2$.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public int distributeCandies(int[] candyType) {
        Set<Integer> uniqueCandies = new HashSet<>();
        for (int type : candyType) {
            uniqueCandies.add(type);
        }
        
        int maxEaten = candyType.length / 2;
        return Math.min(uniqueCandies.size(), maxEaten);
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N)$, với $N$ là số lượng kẹo.
- Không gian (Space Complexity): $O(N)$, khi tất cả các viên kẹo đều khác loại nhau.

---

## 17. N-Repeated Element in Size 2N Array

**Đề bài chi tiết:**
Cho một mảng `nums` có độ dài $2N$. Mảng chứa $N + 1$ phần tử duy nhất, và một trong số các phần tử đó được lặp lại chính xác $N$ lần. Hãy trả về phần tử bị lặp lại $N$ lần đó.

**Phân tích thuật toán:**
Bởi vì có $2N$ phần tử mà trong đó có một phần tử chiếm $N$ chỗ, phần tử nào xuất hiện lần thứ 2 thì chắc chắn đó là đáp án cần tìm. Ta dùng HashSet, duyệt mảng, nếu số đã tồn tại trong set thì trả về số đó.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public int repeatedNTimes(int[] nums) {
        Set<Integer> seen = new HashSet<>();
        for (int num : nums) {
            if (seen.contains(num)) {
                return num;
            }
            seen.add(num);
        }
        return -1;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N)$, trường hợp xấu nhất ta phải duyệt một nửa mảng.
- Không gian (Space Complexity): $O(N)$, cho tập hợp HashSet.

---

## 18. Sort Characters By Frequency

**Đề bài chi tiết:**
Cho một chuỗi `s`, sắp xếp nó theo thứ tự giảm dần dựa trên tần suất xuất hiện của các ký tự. Tần suất của một ký tự là số lần nó xuất hiện trong chuỗi.

**Phân tích thuật toán:**
1. Dùng HashMap để đếm số lần xuất hiện của mỗi ký tự.
2. Dùng Bucket Sort để lưu danh sách các ký tự có cùng số lần xuất hiện. Bucket thứ `i` chứa các ký tự xuất hiện `i` lần.
3. Duyệt từ Bucket cao nhất về 0 để tạo chuỗi kết quả.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

class Solution {
    public String frequencySort(String s) {
        Map<Character, Integer> map = new HashMap<>();
        for (char c : s.toCharArray()) {
            map.put(c, map.getOrDefault(c, 0) + 1);
        }
        
        List<Character>[] bucket = new List[s.length() + 1];
        for (Character key : map.keySet()) {
            int freq = map.get(key);
            if (bucket[freq] == null) {
                bucket[freq] = new ArrayList<>();
            }
            bucket[freq].add(key);
        }
        
        StringBuilder sb = new StringBuilder();
        for (int i = bucket.length - 1; i >= 0; i--) {
            if (bucket[i] != null) {
                for (char c : bucket[i]) {
                    for (int j = 0; j < i; j++) {
                        sb.append(c);
                    }
                }
            }
        }
        return sb.toString();
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N)$, trong đó $N$ là chiều dài của chuỗi `s`.
- Không gian (Space Complexity): $O(N)$, cho HashMap và cấu trúc Bucket.

---

## 19. Number of Good Pairs

**Đề bài chi tiết:**
Cho một mảng các số nguyên `nums`. Một cặp chỉ số $(i, j)$ được gọi là "cặp tốt" (good pair) nếu `nums[i] == nums[j]` và `i < j`. Hãy trả về số lượng các cặp tốt.

**Phân tích thuật toán:**
Nếu ta thấy một số nguyên `x` xuất hiện lần thứ $K$, thì số `x` này có thể tạo thành $K - 1$ cặp với các số `x` đã xuất hiện ở trước đó. Ta duyệt qua mảng, dùng mảng băm để đếm số lượng các số đã gặp, cộng tích lũy số đếm đó vào kết quả rồi tăng tần suất đếm.

**Mã nguồn Java:**
```java
class Solution {
    public int numIdenticalPairs(int[] nums) {
        int[] count = new int[101]; // Theo constraint mảng nums[i] <= 100
        int goodPairs = 0;
        
        for (int num : nums) {
            goodPairs += count[num];
            count[num]++;
        }
        
        return goodPairs;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N)$ với $N$ là số phần tử trong mảng.
- Không gian (Space Complexity): $O(1)$ do mảng đếm kích thước cố định là 101.

---

## 20. Jewels and Stones

**Đề bài chi tiết:**
Cho một chuỗi `jewels` đại diện cho các loại đá quý mà bạn có, và một chuỗi `stones` đại diện cho những viên đá bạn đang sở hữu. Hãy đếm xem có bao nhiêu viên đá của bạn cũng đồng thời là đá quý.

**Phân tích thuật toán:**
Lưu toàn bộ các loại đá quý vào một HashSet để có thể tra cứu với $O(1)$. Duyệt qua từng ký tự trong chuỗi `stones`, nếu ký tự đó tồn tại trong HashSet, ta tăng biến đếm lên 1.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public int numJewelsInStones(String jewels, String stones) {
        Set<Character> jewelSet = new HashSet<>();
        for (char j : jewels.toCharArray()) {
            jewelSet.add(j);
        }
        
        int count = 0;
        for (char s : stones.toCharArray()) {
            if (jewelSet.contains(s)) {
                count++;
            }
        }
        
        return count;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(J + S)$ trong đó $J$ là chiều dài `jewels` và $S$ chiều dài `stones`.
- Không gian (Space Complexity): $O(J)$ để lưu Hash Set của đá quý.

---

## 21. Check If N and Its Double Exist

**Đề bài chi tiết:**
Cho một mảng các số nguyên `arr`, hãy kiểm tra xem có tồn tại hai chỉ số `i` và `j` sao cho `i != j` và `arr[i] == 2 * arr[j]` hay không.

**Phân tích thuật toán:**
Sử dụng HashSet để lưu trữ các phần tử đã duyệt qua. Với mỗi phần tử `x` trong mảng, ta kiểm tra xem `2 * x` có trong HashSet không, hoặc `x / 2` có trong HashSet không (chỉ khi `x` chẵn). Nếu có, ta trả về `true`. Nếu không, ta thêm `x` vào HashSet và tiếp tục.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public boolean checkIfExist(int[] arr) {
        Set<Integer> set = new HashSet<>();
        for (int num : arr) {
            if (set.contains(2 * num) || (num % 2 == 0 && set.contains(num / 2))) {
                return true;
            }
            set.add(num);
        }
        return false;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N)$ với $N$ là số lượng phần tử của mảng.
- Không gian (Space Complexity): $O(N)$ để lưu trữ dữ liệu vào HashSet.

---

## 22. How Many Numbers Are Smaller Than the Current Number

**Đề bài chi tiết:**
Cho mảng `nums`, đối với mỗi phần tử `nums[i]`, hãy đếm xem có bao nhiêu số trong mảng nhỏ hơn nó. Trả về câu trả lời dưới dạng một mảng.

**Phân tích thuật toán:**
Sử dụng mảng đếm tần suất vì theo ràng buộc giá trị của các phần tử thường nhỏ (VD: $0 \le nums[i] \le 100$). Đầu tiên đếm số lần xuất hiện của từng số. Sau đó tính tổng tích lũy (prefix sum) để biết số lượng các số nhỏ hơn một số bất kỳ.

**Mã nguồn Java:**
```java
class Solution {
    public int[] smallerNumbersThanCurrent(int[] nums) {
        int[] count = new int[101];
        for (int num : nums) {
            count[num]++;
        }
        
        for (int i = 1; i <= 100; i++) {
            count[i] += count[i - 1];
        }
        
        int[] result = new int[nums.length];
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == 0) {
                result[i] = 0;
            } else {
                result[i] = count[nums[i] - 1];
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N)$
- Không gian (Space Complexity): $O(1)$ do giới hạn phần tử chỉ từ 0 đến 100 (mảng count độ dài 101).

---

## 23. Unique Number of Occurrences

**Đề bài chi tiết:**
Cho một mảng các số nguyên `arr`, hãy trả về `true` nếu số lần xuất hiện của mỗi giá trị trong mảng là duy nhất, ngược lại trả về `false`.

**Phân tích thuật toán:**
Dùng một HashMap để đếm số lần xuất hiện (tần suất) của từng phần tử trong `arr`. Sau đó, lấy tất cả các tần suất này thêm vào một HashSet. Nếu kích thước của HashSet bằng với số lượng phần tử duy nhất trong HashMap, tức là mọi tần suất đều khác biệt.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;

class Solution {
    public boolean uniqueOccurrences(int[] arr) {
        Map<Integer, Integer> count = new HashMap<>();
        for (int num : arr) {
            count.put(num, count.getOrDefault(num, 0) + 1);
        }
        
        Set<Integer> occurrences = new HashSet<>(count.values());
        return count.size() == occurrences.size();
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N)$
- Không gian (Space Complexity): $O(N)$

---

## 24. Subdomain Visit Count

**Đề bài chi tiết:**
Một tên miền (domain name) như "discuss.leetcode.com" bao gồm nhiều tên miền phụ khác nhau. Ở cấp thấp nhất, có "leetcode.com" và "com". Cho danh sách các chuỗi đếm số lượt truy cập tên miền, hãy trả về danh sách liệt kê số lượt truy cập của mọi tên miền phụ có thể.

**Phân tích thuật toán:**
Duyệt từng phần tử của danh sách, tách số lần truy cập và toàn bộ tên miền. Sử dụng một HashMap để lưu lại số lần truy cập cho mỗi tên miền phụ. Để lấy các tên miền phụ, ta tìm kiếm dấu `.` và lấy toàn bộ chuỗi từ sau dấu chấm đó đến hết.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

class Solution {
    public List<String> subdomainVisits(String[] cpdomains) {
        Map<String, Integer> map = new HashMap<>();
        
        for (String cp : cpdomains) {
            int spaceIndex = cp.indexOf(' ');
            int count = Integer.parseInt(cp.substring(0, spaceIndex));
            String domain = cp.substring(spaceIndex + 1);
            
            map.put(domain, map.getOrDefault(domain, 0) + count);
            for (int i = 0; i < domain.length(); i++) {
                if (domain.charAt(i) == '.') {
                    String sub = domain.substring(i + 1);
                    map.put(sub, map.getOrDefault(sub, 0) + count);
                }
            }
        }
        
        List<String> result = new ArrayList<>();
        for (String key : map.keySet()) {
            result.add(map.get(key) + " " + key);
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N \times L)$, trong đó $N$ là độ dài mảng và $L$ là độ dài tối đa của một chuỗi domain.
- Không gian (Space Complexity): $O(N \times L)$, lưu các domain vào HashMap.

---

## 25. Find Words That Can Be Formed by Characters

**Đề bài chi tiết:**
Cho mảng các chuỗi `words` và một chuỗi `chars`. Một chuỗi được gọi là "tốt" (good) nếu nó có thể được tạo thành bởi các ký tự từ `chars` (mỗi ký tự chỉ được sử dụng một lần). Hãy trả về tổng chiều dài của tất cả các chuỗi tốt trong `words`.

**Phân tích thuật toán:**
Sử dụng một mảng 26 ký tự (Hash Table) để đếm tần suất xuất hiện của các chữ cái trong `chars`. Đối với mỗi từ trong `words`, ta đếm tần suất các ký tự của từ đó, rồi so sánh với mảng đếm của `chars` xem có hợp lệ hay không.

**Mã nguồn Java:**
```java
class Solution {
    public int countCharacters(String[] words, String chars) {
        int[] charCount = new int[26];
        for (char c : chars.toCharArray()) {
            charCount[c - 'a']++;
        }
        
        int result = 0;
        for (String word : words) {
            int[] wordCount = new int[26];
            boolean isGood = true;
            for (char c : word.toCharArray()) {
                wordCount[c - 'a']++;
                if (wordCount[c - 'a'] > charCount[c - 'a']) {
                    isGood = false;
                    break;
                }
            }
            if (isGood) {
                result += word.length();
            }
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N + M \times L)$, với $N$ là chiều dài `chars`, $M$ là số lượng từ trong `words` và $L$ là độ dài tối đa mỗi từ.
- Không gian (Space Complexity): $O(1)$ vì bảng đếm chỉ gồm 26 ký tự.

---

## 26. Destination City

**Đề bài chi tiết:**
Bạn được cung cấp danh sách `paths`, nơi `paths[i] = [cityA, cityB]` có nghĩa là có đường đi trực tiếp một chiều từ thành phố A đến thành phố B. Trả về thành phố đích (destination city), tức là thành phố không có đường đi đến bất kỳ thành phố nào khác. Luôn đảm bảo tồn tại một thành phố đích duy nhất.

**Phân tích thuật toán:**
Một thành phố được gọi là thành phố đích khi và chỉ khi nó là điểm đến của ít nhất một con đường, nhưng không phải là điểm xuất phát của bất kỳ con đường nào. Ta dùng một HashSet để lưu lại toàn bộ các thành phố xuất phát. Sau đó kiểm tra từng thành phố đích, thành phố nào không có trong tập các thành phố xuất phát thì đó là câu trả lời.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.List;
import java.util.Set;

class Solution {
    public String destCity(List<List<String>> paths) {
        Set<String> starts = new HashSet<>();
        for (List<String> path : paths) {
            starts.add(path.get(0));
        }
        
        for (List<String> path : paths) {
            if (!starts.contains(path.get(1))) {
                return path.get(1);
            }
        }
        
        return "";
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N)$, với $N$ là số lượng đoạn đường.
- Không gian (Space Complexity): $O(N)$ lưu HashSet các điểm khởi hành.

---

## 27. Maximum Number of Words You Can Type

**Đề bài chi tiết:**
Có một bàn phím bị hỏng với một số phím chữ cái không hoạt động. Cho chuỗi `text` chứa các từ phân tách bởi khoảng trắng, và một chuỗi `brokenLetters` chứa các chữ cái bị hỏng. Hãy đếm xem bạn có thể gõ được bao nhiêu từ đầy đủ.

**Phân tích thuật toán:**
Đưa tất cả các ký tự bị hỏng trong `brokenLetters` vào một HashSet (hoặc một mảng boolean 26 phần tử). Với mỗi từ trong `text`, duyệt qua từng ký tự, nếu có ký tự nằm trong tập phím hỏng, từ đó không gõ được. Đếm những từ còn lại gõ được thành công.

**Mã nguồn Java:**
```java
class Solution {
    public int canBeTypedWords(String text, String brokenLetters) {
        boolean[] isBroken = new boolean[26];
        for (char c : brokenLetters.toCharArray()) {
            isBroken[c - 'a'] = true;
        }
        
        String[] words = text.split(" ");
        int count = 0;
        
        for (String word : words) {
            boolean canType = true;
            for (char c : word.toCharArray()) {
                if (isBroken[c - 'a']) {
                    canType = false;
                    break;
                }
            }
            if (canType) {
                count++;
            }
        }
        
        return count;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N)$, với $N$ là chiều dài của chuỗi `text`.
- Không gian (Space Complexity): $O(N)$ nếu tính cả mảng lưu các từ phân tách, $O(1)$ cho mảng kiểm tra phím hỏng.

---

## 28. Find Common Characters

**Đề bài chi tiết:**
Cho một mảng các chuỗi `words`. Hãy trả về một mảng chứa mọi ký tự xuất hiện trong tất cả các chuỗi thuộc mảng. Nếu một ký tự xuất hiện nhiều lần ở mọi chuỗi, mảng kết quả phải chứa số lần lặp lại nhỏ nhất của ký tự đó.

**Phân tích thuật toán:**
Dùng một mảng 26 ký tự (Hash Table) để lưu tần suất tối thiểu (minFrequency) của mỗi chữ cái cần để xuất hiện ở mọi chuỗi. Khởi tạo mảng này với tần suất của chuỗi đầu tiên. Duyệt qua các chuỗi còn lại, tính tần suất từng ký tự của chuỗi đó, rồi cập nhật mảng chung bằng hàm `Math.min`. Cuối cùng, tạo mảng kết quả dựa trên số lần xuất hiện nhỏ nhất.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<String> commonChars(String[] words) {
        int[] minFreq = new int[26];
        for (char c : words[0].toCharArray()) {
            minFreq[c - 'a']++;
        }
        
        for (int i = 1; i < words.length; i++) {
            int[] currentFreq = new int[26];
            for (char c : words[i].toCharArray()) {
                currentFreq[c - 'a']++;
            }
            for (int j = 0; j < 26; j++) {
                minFreq[j] = Math.min(minFreq[j], currentFreq[j]);
            }
        }
        
        List<String> result = new ArrayList<>();
        for (int i = 0; i < 26; i++) {
            while (minFreq[i] > 0) {
                result.add(String.valueOf((char) (i + 'a')));
                minFreq[i]--;
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N \times L)$, trong đó $N$ là số chuỗi, $L$ là độ dài trung bình mỗi chuỗi.
- Không gian (Space Complexity): $O(1)$, vì các mảng tần suất luôn có kích thước cố định là 26.

---

## 29. Check if All Characters Have Equal Number of Occurrences

**Đề bài chi tiết:**
Cho một chuỗi `s`, trả về `true` nếu mọi ký tự xuất hiện trong chuỗi đều có cùng số lần xuất hiện, ngược lại trả về `false`.

**Phân tích thuật toán:**
Sử dụng Hash Map (hoặc mảng 26 phần tử) để đếm số lần xuất hiện của tất cả các ký tự. Sau đó lấy số lần xuất hiện của ký tự đầu tiên có số lượng $>0$ làm chuẩn, rồi so sánh với các ký tự có số lượng $>0$ khác.

**Mã nguồn Java:**
```java
class Solution {
    public boolean areOccurrencesEqual(String s) {
        int[] count = new int[26];
        for (char c : s.toCharArray()) {
            count[c - 'a']++;
        }
        
        int freq = 0;
        for (int c : count) {
            if (c > 0) {
                if (freq == 0) {
                    freq = c;
                } else if (freq != c) {
                    return false;
                }
            }
        }
        
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N)$, duyệt qua chuỗi `s` một lần.
- Không gian (Space Complexity): $O(1)$, dùng mảng kích thước cố định 26.

---

## 30. Two Out of Three

**Đề bài chi tiết:**
Cho ba mảng số nguyên `nums1`, `nums2`, `nums3`. Trả về một mảng chứa tất cả các giá trị tồn tại ở ít nhất 2 trong 3 mảng trên.

**Phân tích thuật toán:**
Để giải quyết bài toán này, ta sử dụng 3 bộ HashSet để lọc các giá trị trùng lặp nội bộ trong mỗi mảng. Sau đó tạo một mảng đếm tần suất chung (hoặc dùng Map). Duyệt qua từng tập Set, cộng biến đếm của phần tử đó lên 1. Phần tử nào có đếm $\ge 2$ thì thêm vào kết quả.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.HashSet;
import java.util.List;
import java.util.Map;
import java.util.Set;

class Solution {
    public List<Integer> twoOutOfThree(int[] nums1, int[] nums2, int[] nums3) {
        Map<Integer, Integer> countMap = new HashMap<>();
        
        Set<Integer> set1 = new HashSet<>();
        for (int num : nums1) set1.add(num);
        
        Set<Integer> set2 = new HashSet<>();
        for (int num : nums2) set2.add(num);
        
        Set<Integer> set3 = new HashSet<>();
        for (int num : nums3) set3.add(num);
        
        for (int num : set1) countMap.put(num, countMap.getOrDefault(num, 0) + 1);
        for (int num : set2) countMap.put(num, countMap.getOrDefault(num, 0) + 1);
        for (int num : set3) countMap.put(num, countMap.getOrDefault(num, 0) + 1);
        
        List<Integer> result = new ArrayList<>();
        for (Map.Entry<Integer, Integer> entry : countMap.entrySet()) {
            if (entry.getValue() >= 2) {
                result.add(entry.getKey());
            }
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N_1 + N_2 + N_3)$, do duyệt từng phần tử của 3 mảng.
- Không gian (Space Complexity): $O(N_1 + N_2 + N_3)$, để lưu trữ các HashSets và HashMap.
