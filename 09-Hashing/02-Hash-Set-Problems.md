# Hash Set: Danh Sách Bài Tập Thực Hành

Tài liệu này bao gồm 30 bài tập ứng dụng cấu trúc dữ liệu Hash Set (Tập Hợp Băm), từ cơ bản đến nâng cao. 10 bài đầu tiên có lời giải chi tiết bằng Java, phân tích thuật toán và đánh giá độ phức tạp. 20 bài tiếp theo được tóm tắt ý tưởng.

---

## 1. Longest Consecutive Sequence (Dãy liên tiếp dài nhất)

**Đề bài chi tiết:**
Cho một mảng các số nguyên chưa sắp xếp `nums`. Hãy trả về độ dài của dãy các phần tử liên tiếp dài nhất. Thuật toán của bạn phải chạy trong thời gian O(n).

**Phân tích thuật toán:**
Để đạt được O(n), ta có thể đẩy tất cả các phần tử vào một Hash Set. Sau đó duyệt qua từng số nguyên trong Set. Để tối ưu, ta chỉ bắt đầu đếm chiều dài dãy khi số hiện tại là "điểm bắt đầu" của một dãy liên tiếp (tức là `num - 1` không tồn tại trong Set). Từ "điểm bắt đầu" này, ta liên tục kiểm tra `num + 1, num + 2,...` xem có trong Set hay không.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int longestConsecutive(int[] nums) {
        Set<Integer> numSet = new HashSet<>();
        for (int num : nums) {
            numSet.add(num);
        }

        int longestStreak = 0;

        for (int num : numSet) {
            // Chỉ bắt đầu đếm khi số hiện tại là điểm khởi đầu của dãy
            if (!numSet.contains(num - 1)) {
                int currentNum = num;
                int currentStreak = 1;

                while (numSet.contains(currentNum + 1)) {
                    currentNum += 1;
                    currentStreak += 1;
                }

                longestStreak = Math.max(longestStreak, currentStreak);
            }
        }

        return longestStreak;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N), vì mỗi phần tử được truy xuất trong Set số lần hằng số.
- Không gian (Space): O(N), để lưu trữ N phần tử trong Hash Set.

---

## 2. Happy Number (Số Hạnh Phúc)

**Đề bài chi tiết:**
Viết thuật toán để xác định xem số `n` có phải là "Happy Number" hay không.
Số hạnh phúc là một số được định nghĩa theo quy trình sau:
- Bắt đầu với số nguyên dương bất kỳ, thay thế số đó bằng tổng bình phương các chữ số của nó.
- Lặp lại quá trình này cho đến khi số bằng 1, hoặc nó lặp vô tận trong một chu kỳ không bao gồm 1.
Nếu kết quả cuối cùng là 1, số ban đầu là Happy Number.

**Phân tích thuật toán:**
Trong quá trình tính toán tổng bình phương các chữ số, chúng ta có thể rơi vào vòng lặp vô hạn. Do đó, cần lưu lại các giá trị tổng đã từng xuất hiện vào một Hash Set. Nếu tính ra một tổng mới mà tổng đó đã nằm trong Set (nghĩa là đã tạo thành vòng lặp), ta kết luận đây không phải là Happy Number.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
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
- Thời gian (Time): O(log N), số chữ số của một số là log(N), và số vòng lặp tối đa là giới hạn hữu hạn.
- Không gian (Space): O(log N), lượng số được thêm vào Set.

---

## 3. Intersection of Two Arrays (Giao của hai mảng)

**Đề bài chi tiết:**
Cho hai mảng số nguyên `nums1` và `nums2`, hãy trả về một mảng chứa giao của chúng (phần tử xuất hiện trong cả hai mảng). Mỗi phần tử trong kết quả phải là duy nhất. Bạn có thể trả về mảng kết quả theo bất kỳ thứ tự nào.

**Phân tích thuật toán:**
Dùng một Hash Set đầu tiên để lưu toàn bộ các số duy nhất từ `nums1`. Dùng Hash Set thứ hai để lặp qua `nums2`, kiểm tra nếu số trong `nums2` có tồn tại trong Hash Set đầu tiên, thì đẩy số đó vào Hash Set kết quả (đảm bảo tính duy nhất).

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> set1 = new HashSet<>();
        for (int num : nums1) {
            set1.add(num);
        }

        Set<Integer> intersect = new HashSet<>();
        for (int num : nums2) {
            if (set1.contains(num)) {
                intersect.add(num);
            }
        }

        int[] result = new int[intersect.size()];
        int i = 0;
        for (int num : intersect) {
            result[i++] = num;
        }
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(M + N), với M, N là kích thước 2 mảng.
- Không gian (Space): O(M + K), M cho Set đầu tiên và K phần tử giao trong Set kết quả.

---

## 4. Jewels and Stones (Đá quý và Sỏi)

**Đề bài chi tiết:**
Bạn được cung cấp một chuỗi `jewels` biểu thị các loại đá quý, và chuỗi `stones` biểu thị những viên đá bạn đang sở hữu. Bạn muốn biết bao nhiêu viên đá của bạn cũng là đá quý. Các kí tự có phân biệt chữ hoa, chữ thường (ví dụ 'a' khác 'A').

**Phân tích thuật toán:**
Đưa tất cả các kí tự trong chuỗi `jewels` vào một Hash Set. Sau đó duyệt qua chuỗi `stones` và tăng bộ đếm nếu kí tự đá đó tồn tại trong tập hợp `jewels`.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
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
- Thời gian (Time): O(J + S), J là số đá quý, S là số lượng đá bạn có.
- Không gian (Space): O(J) cho bộ nhớ chứa loại đá quý. Kích thước tối đa là 52 kí tự.

---

## 5. Find All Numbers Disappeared in an Array (Tìm các số biến mất)

**Đề bài chi tiết:**
Cho một mảng `nums` gồm n số nguyên, với giá trị mỗi số nằm trong khoảng [1, n]. Hãy trả về một mảng chứa tất cả các số nguyên nằm trong khoảng [1, n] nhưng không xuất hiện trong `nums`.

**Phân tích thuật toán:**
Cách trực quan nhất là dùng một Hash Set lưu toàn bộ phần tử trong mảng. Sau đó duyệt từ `i = 1` đến `n`, kiểm tra nếu `i` không có trong Hash Set thì thêm nó vào danh sách kết quả. (Lưu ý: Có giải pháp không dùng Set đạt O(1) Space, nhưng ở bài này chúng ta luyện tập Hash Set).

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

public class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        Set<Integer> numSet = new HashSet<>();
        for (int num : nums) {
            numSet.add(num);
        }
        
        List<Integer> result = new ArrayList<>();
        for (int i = 1; i <= nums.length; i++) {
            if (!numSet.contains(i)) {
                result.add(i);
            }
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N), duyệt mảng và duyệt từ 1 đến N.
- Không gian (Space): O(N), lưu N phần tử vào Hash Set.

---

## 6. Unique Number of Occurrences (Số lần xuất hiện duy nhất)

**Đề bài chi tiết:**
Cho một mảng số nguyên `arr`, viết hàm trả về `true` nếu số lần xuất hiện của từng giá trị trong mảng đều là duy nhất, ngược lại trả về `false`.

**Phân tích thuật toán:**
Bước 1: Dùng HashMap đếm số lần xuất hiện của mỗi phần tử.
Bước 2: Lấy tất cả các giá trị (số lần xuất hiện) từ HashMap cho vào HashSet. Nếu quá trình thêm phát hiện giá trị đã tồn tại (hoặc kích thước Set nhỏ hơn kích thước Map) thì suy ra có trùng lặp tần suất.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;

public class Solution {
    public boolean uniqueOccurrences(int[] arr) {
        Map<Integer, Integer> countMap = new HashMap<>();
        for (int num : arr) {
            countMap.put(num, countMap.getOrDefault(num, 0) + 1);
        }
        
        Set<Integer> uniqueCounts = new HashSet<>();
        for (int count : countMap.values()) {
            if (!uniqueCounts.add(count)) {
                return false;
            }
        }
        
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N).
- Không gian (Space): O(N), do dùng cả HashMap và HashSet (trường hợp xấu nhất).

---

## 7. Distribute Candies (Chia kẹo)

**Đề bài chi tiết:**
Cô bé Alice nhận được `n` viên kẹo, loại kẹo thứ `i` được đại diện bởi số nguyên `candyType[i]`. Alice muốn ăn tối đa n/2 viên kẹo (để bảo vệ sức khỏe). Hãy tìm số lượng loại kẹo lớn nhất mà cô bé có thể ăn.

**Phân tích thuật toán:**
Số lượng kẹo Alice có thể ăn là giới hạn cứng: `N / 2`.
Tuy nhiên, cô bé chỉ ăn được tối đa số loại kẹo tồn tại (các số phân biệt trong mảng).
Do đó, số loại kẹo lớn nhất bằng mức nhỏ hơn giữa số loại kẹo duy nhất (HashSet size) và giới hạn lượng kẹo ăn được `N / 2`.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int distributeCandies(int[] candyType) {
        Set<Integer> uniqueCandies = new HashSet<>();
        for (int candy : candyType) {
            uniqueCandies.add(candy);
        }
        
        int maxEatable = candyType.length / 2;
        return Math.min(uniqueCandies.size(), maxEatable);
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N).
- Không gian (Space): O(N).

---

## 8. Design HashString (Thiết kế tập hợp chuỗi)

**Đề bài chi tiết:**
Thiết kế một cấu trúc dữ liệu đơn giản giống như `HashSet` cho các Chuỗi, hỗ trợ các thao tác `add(String str)`, `contains(String str)` và `remove(String str)`. Không sử dụng Hash Set có sẵn, dùng LinkedList và cấu trúc Hash Table tự dựng.

**Phân tích thuật toán:**
Tạo một mảng các thùng chứa (buckets) với kích thước cố định. Hàm băm sử dụng `str.hashCode() % size` (lưu ý giá trị tuyệt đối để tránh mảng chỉ số âm). Tại mỗi bucket, ta dùng `LinkedList` xử lý va chạm bằng cơ chế Separate Chaining.

**Mã nguồn Java:**
```java
import java.util.LinkedList;

public class MyStringHashSet {
    private final int BUCKET_SIZE = 769;
    private LinkedList<String>[] buckets;

    public MyStringHashSet() {
        buckets = new LinkedList[BUCKET_SIZE];
        for (int i = 0; i < BUCKET_SIZE; i++) {
            buckets[i] = new LinkedList<>();
        }
    }

    private int getHash(String key) {
        return Math.abs(key.hashCode() % BUCKET_SIZE);
    }

    public void add(String key) {
        int index = getHash(key);
        if (!buckets[index].contains(key)) {
            buckets[index].add(key);
        }
    }

    public void remove(String key) {
        int index = getHash(key);
        buckets[index].remove(key);
    }

    public boolean contains(String key) {
        int index = getHash(key);
        return buckets[index].contains(key);
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): Trung bình O(1), xấu nhất O(N) nếu chuỗi dồn hết vào 1 bucket.
- Không gian (Space): O(N).

---

## 9. Longest Palindrome (Chuỗi đối xứng dài nhất)

**Đề bài chi tiết:**
Cho một chuỗi `s` bao gồm các chữ cái hoa và thường. Hãy tìm độ dài chuỗi đối xứng (Palindrome) dài nhất có thể xây dựng từ các kí tự trong chuỗi `s`. Kí tự phân biệt hoa thường.

**Phân tích thuật toán:**
Chuỗi đối xứng cần các kí tự xuất hiện theo từng cặp (chẵn). Có thể có 1 kí tự dư đứng lẻ giữa chuỗi.
Dùng một HashSet để duyệt mảng: Nếu kí tự chưa có trong Set, thêm nó vào. Nếu kí tự đã có trong Set, nghĩa là ta vừa tìm được 1 cặp, ta tăng độ dài thêm 2, và bỏ kí tự ra khỏi Set. Cuối cùng, nếu Set không rỗng (còn kí tự lẻ), độ dài chuỗi có thể + 1 (cho chữ đứng giữa).

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int longestPalindrome(String s) {
        Set<Character> charSet = new HashSet<>();
        int length = 0;
        
        for (char c : s.toCharArray()) {
            if (charSet.contains(c)) {
                // Đã đủ 1 cặp
                charSet.remove(c);
                length += 2;
            } else {
                charSet.add(c);
            }
        }
        
        // Nếu Set không rỗng, ta có thể đặt 1 kí tự vào giữa chuỗi đối xứng
        if (!charSet.isEmpty()) {
            length += 1;
        }
        
        return length;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N), duyệt chuỗi một lần.
- Không gian (Space): O(1), kích thước HashSet tối đa là 52 (bảng chữ cái tiếng Anh).

---

## 10. Contains Duplicate II (Chứa phần tử lặp lại ở khoảng cách gần)

**Đề bài chi tiết:**
Cho một mảng `nums` và một số nguyên `k`, trả về `true` nếu tồn tại hai chỉ số `i` và `j` khác nhau sao cho `nums[i] == nums[j]` và `abs(i - j) <= k`.

**Phân tích thuật toán:**
Dùng kỹ thuật Sliding Window kết hợp Hash Set. Hash Set sẽ duy trì một "cửa sổ" kích thước tối đa là `k`. Duyệt qua mảng:
- Nếu số hiện tại đã có trong Set -> thỏa mãn điều kiện, trả về `true`.
- Thêm số vào Set.
- Nếu kích thước Set lớn hơn `k`, tiến hành loại bỏ phần tử cũ nhất ở chỉ số `i - k` khỏi Set.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
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

**Độ phức tạp:**
- Thời gian (Time): O(N).
- Không gian (Space): O(K), cửa sổ lưu tối đa K phần tử.

---

## 11. Contains Duplicate (Kiểm tra mảng có phần tử trùng lặp)

**Đề bài chi tiết:**
Cho một mảng số nguyên `nums`. Trả về `true` nếu có ít nhất một giá trị xuất hiện ít nhất hai lần trong mảng, và trả về `false` nếu mọi phần tử trong mảng đều phân biệt.

**Phân tích thuật toán:**
Sử dụng Hash Set để lưu trữ các phần tử đã duyệt qua trong mảng `nums`. Duyệt qua từng phần tử của mảng, nếu phần tử đó đã tồn tại trong Hash Set, điều đó có nghĩa là có phần tử trùng lặp và ta trả về `true`. Nếu duyệt hết mảng mà không có phần tử nào trùng lặp, trả về `false`. Việc thêm và tra cứu trên Hash Set có độ phức tạp trung bình là O(1).

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
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
- Thời gian (Time): O(N), duyệt mảng kích thước N một lần.
- Không gian (Space): O(N), trường hợp xấu nhất không có phần tử trùng lặp, Hash Set lưu toàn bộ N phần tử.

---

## 12. Single Number (Số đơn lẻ)

**Đề bài chi tiết:**
Cho một mảng số nguyên khác rỗng `nums`, mỗi phần tử xuất hiện hai lần ngoại trừ một phần tử duy nhất xuất hiện một lần. Hãy tìm phần tử duy nhất đó. Thuật toán của bạn phải có độ phức tạp thời gian tuyến tính và chỉ sử dụng thêm không gian bộ nhớ hằng số O(1). Dù vậy, trong bài này ta sẽ thực hành cách giải sử dụng Hash Set.

**Phân tích thuật toán:**
Dùng một Hash Set. Khi duyệt qua từng số nguyên trong mảng, nếu số đó chưa có trong Set thì thêm vào, nếu đã có thì xóa khỏi Set. Do mọi số khác đều xuất hiện hai lần nên chúng sẽ được thêm vào rồi lại bị xóa. Kết thúc quá trình, Set sẽ chỉ còn đúng 1 phần tử duy nhất là số xuất hiện một lần.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int singleNumber(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) {
            if (set.contains(num)) {
                set.remove(num);
            } else {
                set.add(num);
            }
        }
        // Phần tử duy nhất còn lại trong Set là kết quả
        return set.iterator().next();
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N), vì duyệt qua mảng kích thước N và thao tác thêm/xóa trong Set mất O(1).
- Không gian (Space): O(N) cho Hash Set trong cách làm này. (Nếu muốn O(1) Space thì dùng phép XOR: `result ^= num`).

---

## 13. Word Pattern (Mẫu từ)

**Đề bài chi tiết:**
Cho một chuỗi mẫu `pattern` và một chuỗi `s`, kiểm tra xem `s` có tuân theo đúng mẫu `pattern` đó hay không. Ở đây, "tuân theo" có nghĩa là có một ánh xạ 1-1 (bijection) giữa một chữ cái trong `pattern` và một từ không rỗng trong `s`.

**Phân tích thuật toán:**
Chia chuỗi `s` thành các từ (ngăn cách bởi dấu cách). Nếu số lượng kí tự trong `pattern` khác số lượng từ trong `s` thì trả về `false`.
Sử dụng một Hash Map kết hợp với một Hash Set. Dùng Hash Map để ánh xạ `character -> word`. Khi gặp một `character` mới, ta cần kiểm tra xem `word` này đã được ánh xạ bởi một `character` nào khác chưa bằng cách sử dụng Hash Set chứa các `word` đã dùng.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;

public class Solution {
    public boolean wordPattern(String pattern, String s) {
        String[] words = s.split(" ");
        if (pattern.length() != words.length) {
            return false;
        }
        
        Map<Character, String> charToWord = new HashMap<>();
        Set<String> usedWords = new HashSet<>();
        
        for (int i = 0; i < pattern.length(); i++) {
            char c = pattern.charAt(i);
            String word = words[i];
            
            if (charToWord.containsKey(c)) {
                if (!charToWord.get(c).equals(word)) {
                    return false;
                }
            } else {
                if (usedWords.contains(word)) {
                    return false;
                }
                charToWord.put(c, word);
                usedWords.add(word);
            }
        }
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N) trong đó N là chiều dài của chuỗi `pattern` (hoặc số từ của `s`), giả sử thao tác chuỗi tốn thời gian tối thiểu.
- Không gian (Space): O(M) với M là số lượng từ duy nhất đưa vào HashSet và HashMap (hoặc O(N) trong trường hợp xấu nhất).

---

## 14. Valid Sudoku (Bảng Sudoku hợp lệ)

**Đề bài chi tiết:**
Xác định xem một bảng Sudoku `9 x 9` có hợp lệ hay không. Chỉ cần kiểm tra các ô đã điền số theo các quy tắc:
1. Mỗi hàng phải chứa các chữ số `1-9` không lặp lại.
2. Mỗi cột phải chứa các chữ số `1-9` không lặp lại.
3. Mỗi trong 9 ô vuông con (sub-boxes) `3 x 3` của lưới cũng phải chứa các chữ số `1-9` không lặp lại.

**Phân tích thuật toán:**
Khởi tạo một Hash Set kiểu String để lưu lại trạng thái các số đã gặp.
Với mỗi số `val` tại hàng `i` cột `j`, ta định dạng 3 chuỗi đại diện:
- Giá trị ở hàng `i`: "row_" + i + "_" + val
- Giá trị ở cột `j`: "col_" + j + "_" + val
- Giá trị ở sub-box thứ `i/3, j/3`: "box_" + (i/3) + "_" + (j/3) + "_" + val
Nếu thêm bất kỳ chuỗi nào vào HashSet bị thất bại (trả về `false`), nghĩa là Sudoku lỗi do có sự trùng lặp.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public boolean isValidSudoku(char[][] board) {
        Set<String> seen = new HashSet<>();
        
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                char val = board[i][j];
                if (val != '.') {
                    String rowId = "row_" + i + "_" + val;
                    String colId = "col_" + j + "_" + val;
                    String boxId = "box_" + (i / 3) + "_" + (j / 3) + "_" + val;
                    
                    if (!seen.add(rowId) || !seen.add(colId) || !seen.add(boxId)) {
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
- Thời gian (Time): O(1) vì mảng có kích thước cố định 9x9 (duyệt đúng 81 ô).
- Không gian (Space): O(1) tối đa lưu trữ được 3 * 81 = 243 chuỗi trạng thái trong Set.

---

## 15. Set Mismatch (Lỗi trong Tập hợp)

**Đề bài chi tiết:**
Bạn có một tập hợp các số nguyên `s`, vốn dĩ chứa tất cả các số từ `1` đến `n`. Nhưng do lỗi dữ liệu, một trong các số trong tập hợp đã bị trùng lặp bằng một số khác trong tập hợp, dẫn đến một số xuất hiện hai lần và một số bị mất. Cho mảng `nums` đại diện cho dữ liệu của tập hợp sau lỗi, hãy tìm ra số bị trùng và số bị mất. Trả về chúng dưới dạng mảng.

**Phân tích thuật toán:**
Dùng Hash Set để tìm phần tử bị lặp lại. Ta duyệt qua mảng `nums` và thêm từng phần tử vào Hash Set, nếu thao tác thêm `add()` thất bại, phần tử đó chính là số bị trùng.
Để tìm số bị mất, ta có thể duyệt từ `1` đến `n` (kích thước của mảng `nums`), kiểm tra số nào không tồn tại trong Set thì đó là số bị mất.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int[] findErrorNums(int[] nums) {
        Set<Integer> set = new HashSet<>();
        int duplicate = -1;
        
        for (int num : nums) {
            if (!set.add(num)) {
                duplicate = num;
            }
        }
        
        int missing = -1;
        for (int i = 1; i <= nums.length; i++) {
            if (!set.contains(i)) {
                missing = i;
                break;
            }
        }
        
        return new int[]{duplicate, missing};
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N), duyệt mảng `nums` và duyệt lại từ 1 đến N.
- Không gian (Space): O(N), lưu các số vào Hash Set.

---

## 16. Check If N and Its Double Exist (Kiểm tra N và 2N)

**Đề bài chi tiết:**
Cho một mảng các số nguyên `arr`, kiểm tra xem có tồn tại hai chỉ số `i` và `j` sao cho `i != j` và `arr[i] == 2 * arr[j]` hay không. Nếu có trả về `true`, ngược lại `false`.

**Phân tích thuật toán:**
Duyệt qua mảng từ trái qua phải, ta dùng một Hash Set lưu lại các phần tử đã duyệt. Ở mỗi phần tử `num` hiện tại, ta kiểm tra xem `2 * num` có nằm trong Set không (trường hợp hiện tại đóng vai trò là N, trong Set là 2N), và kiểm tra xem `num % 2 == 0` và `num / 2` có nằm trong Set không (trường hợp hiện tại là 2N, trong Set là N).
Cách này đảm bảo kiểm tra ngay trên một đường đi và xử lý đúng `i != j`.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public boolean checkIfExist(int[] arr) {
        Set<Integer> seen = new HashSet<>();
        for (int num : arr) {
            if (seen.contains(num * 2) || (num % 2 == 0 && seen.contains(num / 2))) {
                return true;
            }
            seen.add(num);
        }
        return false;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N) do duyệt mảng 1 lần, các thao tác Set là O(1).
- Không gian (Space): O(N), lưu tối đa N phần tử vào Set.

---

## 17. First Missing Positive (Số nguyên dương thiếu nhỏ nhất)

**Đề bài chi tiết:**
Cho một mảng số nguyên `nums` chưa sắp xếp, hãy tìm số nguyên dương nhỏ nhất không tồn tại trong mảng.
Yêu cầu thuật toán phải chạy trong thời gian O(n) và sử dụng thêm không gian bộ nhớ O(1). Trong bài tập này, ta sử dụng Hash Set để luyện tập với cấu trúc dữ liệu Set (Không gian O(n)). (Cách tối ưu In-place xin dành cho một chủ đề khác).

**Phân tích thuật toán:**
Vì cần tìm số nguyên dương (1, 2, 3...) thiếu nhỏ nhất, ta đưa tất cả các phần tử dương của mảng `nums` vào một Hash Set.
Sau đó bắt đầu kiểm tra từ số `i = 1` tăng dần lên. Nếu Set không chứa `i` thì đó chính là số dương nhỏ nhất bị thiếu.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int firstMissingPositive(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) {
            // Chỉ cần lưu các số nguyên dương
            if (num > 0) {
                set.add(num);
            }
        }
        
        int missing = 1;
        while (set.contains(missing)) {
            missing++;
        }
        
        return missing;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N), duyệt mảng kích thước N để đưa vào Set, vòng lặp while chạy tối đa N lần vì câu trả lời luôn <= N + 1.
- Không gian (Space): O(N), lưu các số dương vào Set.

---

## 18. Count Primes (Đếm số lượng số nguyên tố)

**Đề bài chi tiết:**
Đếm số lượng các số nguyên tố nhỏ hơn số nguyên không âm `n`.

**Phân tích thuật toán:**
Dùng thuật toán Sàng Eratosthenes (Sieve of Eratosthenes). Có thể dùng mảng `boolean[]` để đánh dấu tối ưu nhất, tuy nhiên trong khuôn khổ bài học về Set, ta có thể dùng HashSet để lưu các hợp số (composite numbers).
Duyệt `i` từ 2 đến căn bậc hai của `n`. Nếu `i` không nằm trong Set (chưa bị đánh dấu là hợp số), nó là số nguyên tố. Ta bỏ các bội của `i` (`i*i`, `i*i+i`, ...) vào HashSet. Cuối cùng, những số từ 2 đến `n-1` không nằm trong Set chính là số nguyên tố. *(Lưu ý: Mảng Boolean nhanh hơn Set rất nhiều, nhưng Set vẫn thể hiện đúng tính logic).*

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int countPrimes(int n) {
        if (n <= 2) return 0;
        
        Set<Integer> compositeSet = new HashSet<>();
        
        // Sàng Eratosthenes
        for (int i = 2; i * i < n; i++) {
            if (!compositeSet.contains(i)) {
                for (int j = i * i; j < n; j += i) {
                    compositeSet.add(j);
                }
            }
        }
        
        int count = 0;
        for (int i = 2; i < n; i++) {
            if (!compositeSet.contains(i)) {
                count++;
            }
        }
        
        return count;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N log(log N)) theo tính chất sàng Eratosthenes. Nhưng do overhead của HashSet nên hằng số chi phí khá lớn.
- Không gian (Space): O(N) do lưu các hợp số nhỏ hơn n vào Set.

---

## 19. Number of Equivalent Domino Pairs (Đếm số cặp Domino)

**Đề bài chi tiết:**
Cho một danh sách các Domino, `dominoes[i] = [a, b]` (đại diện cho số lượng dấu chấm trên hai nửa của Domino). Hai Domino `dominoes[i]` và `dominoes[j]` được coi là tương đương nếu (`a == c` và `b == d`) hoặc (`a == d` và `b == c`). Hãy đếm số lượng cặp `(i, j)` thỏa mãn `i < j` sao cho chúng tương đương nhau.

**Phân tích thuật toán:**
Ta cần chuẩn hóa mỗi domino về một dạng chung để dễ so sánh: luân chuyển cặp `[a, b]` sao cho số nhỏ đứng trước, lớn đứng sau (VD: `[3, 1]` thành `[1, 3]`). Do `1 <= a, b <= 9`, ta có thể chuyển mỗi domino thành số nguyên 2 chữ số: `min(a, b) * 10 + max(a, b)`. Dùng Hash Map để lưu tần suất xuất hiện của các Domino chuẩn hóa này. Nếu một domino dạng chuẩn xuất hiện `k` lần, số cặp tạo ra được là `k * (k - 1) / 2`. Ta cộng dồn số lượng này.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int numEquivDominoPairs(int[][] dominoes) {
        Map<Integer, Integer> countMap = new HashMap<>();
        
        for (int[] domino : dominoes) {
            int a = domino[0];
            int b = domino[1];
            // Chuẩn hóa
            int key = Math.min(a, b) * 10 + Math.max(a, b);
            countMap.put(key, countMap.getOrDefault(key, 0) + 1);
        }
        
        int pairs = 0;
        for (int count : countMap.values()) {
            if (count > 1) {
                pairs += count * (count - 1) / 2;
            }
        }
        
        return pairs;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N), duyệt một lần qua danh sách dominoes (kích thước N).
- Không gian (Space): O(1), do cặp 2 chữ số tối đa từ 11 đến 99, Hash Map chứa tối đa 45 phần tử.

---

## 20. Finding the Users Active Minutes (Phút hoạt động của User)

**Đề bài chi tiết:**
Cho một mảng `logs` trong đó `logs[i] = [ID_người_dùng, thời_gian]`. "Phút hoạt động của người dùng" (UAM - User Active Minutes) được định nghĩa là số lượng phút duy nhất mà người dùng thực hiện một hành động (nhiều hành động trong cùng 1 phút chỉ tính là 1).
Hãy tính một mảng `answer` kích thước `k` (1-indexed), trong đó `answer[j]` là số lượng người dùng có UAM bằng `j + 1`.

**Phân tích thuật toán:**
Sử dụng Hash Map để nhóm dữ liệu theo từng ID người dùng. Value của Map sẽ là một Hash Set để đếm số phút DUY NHẤT mà người dùng đó hoạt động.
Sau khi phân tích xong toàn bộ log, ta duyệt qua từng người dùng trong Map, lấy kích thước của Hash Set (chính là số phút UAM) và tăng biến đếm tương ứng ở vị trí `UAM - 1` trong mảng `answer`.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;

public class Solution {
    public int[] findingUsersActiveMinutes(int[][] logs, int k) {
        Map<Integer, Set<Integer>> userActiveMins = new HashMap<>();
        
        for (int[] log : logs) {
            int userId = log[0];
            int minute = log[1];
            userActiveMins.putIfAbsent(userId, new HashSet<>());
            userActiveMins.get(userId).add(minute);
        }
        
        int[] answer = new int[k];
        for (Set<Integer> minutesSet : userActiveMins.values()) {
            int uam = minutesSet.size();
            if (uam >= 1 && uam <= k) {
                answer[uam - 1]++;
            }
        }
        
        return answer;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N), với N là số lượng logs. Các thao tác trên HashSet và HashMap tốn O(1).
- Không gian (Space): O(N) trong trường hợp tất cả log đều riêng biệt và cần phải lưu vào Hash Map / Hash Set.

---

## 21. Keyboard Row (Hàng phím)

**Đề bài chi tiết:**
Cho một mảng các chuỗi `words`, hãy trả về các từ có thể được gõ chỉ bằng các chữ cái trên một hàng của bàn phím QWERTY của Mỹ.
Ba hàng bàn phím bao gồm:
- Hàng 1: "qwertyuiop"
- Hàng 2: "asdfghjkl"
- Hàng 3: "zxcvbnm"

**Phân tích thuật toán:**
Sử dụng 3 chuỗi đại diện cho 3 hàng hoặc một bảng băm, mảng lưu trữ chỉ mục hàng của từng chữ cái. Trong bài này để ứng dụng Set, ta dùng 3 Hash Set lưu các kí tự (cả hoa và thường) của mỗi hàng. Khi xét một từ, ta xác định xem kí tự đầu tiên thuộc Set nào, sau đó kiểm tra xem toàn bộ các kí tự còn lại của từ đó có thuộc cùng Set đó hay không. Nếu có, từ đó hợp lệ.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

public class Solution {
    public String[] findWords(String[] words) {
        String row1 = "qwertyuiopQWERTYUIOP";
        String row2 = "asdfghjklASDFGHJKL";
        String row3 = "zxcvbnmZXCVBNM";
        
        Set<Character> set1 = new HashSet<>();
        Set<Character> set2 = new HashSet<>();
        Set<Character> set3 = new HashSet<>();
        
        for (char c : row1.toCharArray()) set1.add(c);
        for (char c : row2.toCharArray()) set2.add(c);
        for (char c : row3.toCharArray()) set3.add(c);
        
        List<String> result = new ArrayList<>();
        
        for (String word : words) {
            if (word.isEmpty()) continue;
            
            char firstChar = word.charAt(0);
            Set<Character> targetSet;
            if (set1.contains(firstChar)) targetSet = set1;
            else if (set2.contains(firstChar)) targetSet = set2;
            else targetSet = set3;
            
            boolean isValid = true;
            for (char c : word.toCharArray()) {
                if (!targetSet.contains(c)) {
                    isValid = false;
                    break;
                }
            }
            if (isValid) {
                result.add(word);
            }
        }
        
        return result.toArray(new String[0]);
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N * M), với N là số lượng từ, M là độ dài trung bình của từ. Việc truy vấn trong Set tốn O(1).
- Không gian (Space): O(1) do số lượng kí tự trong Set không đổi (bảng chữ cái tiếng Anh). Mảng trả về không được tính trong độ phức tạp không gian phụ.

---

## 22. Bulls and Cows (Trò chơi bò và gà)

**Đề bài chi tiết:**
Bạn đang chơi trò chơi "Bulls and Cows" với bạn của mình. Bạn viết ra một con số bí mật và yêu cầu bạn của mình đoán.
- Cứ mỗi chữ số đoán đúng vị trí và đúng giá trị thì được 1 điểm "Bull".
- Cứ mỗi chữ số có mặt trong số bí mật nhưng bị sai vị trí thì được 1 điểm "Cow".
Trả về gợi ý dưới dạng chuỗi "xAyB" với x là số Bulls, y là số Cows. (Các kí tự là các số từ '0' đến '9').

**Phân tích thuật toán:**
Dùng mảng đánh dấu tần suất để tối ưu, hoặc dùng Hash Map / Hash Set. Do các số chỉ từ 0 đến 9, dùng 1 mảng tĩnh `int[] count = new int[10]` là giải pháp tốt nhất. Duyệt qua hai chuỗi:
- Nếu `secret.charAt(i) == guess.charAt(i)`, đếm tăng Bull.
- Ngược lại, nếu chưa khớp, ta thay đổi tần suất. Tăng tần suất cho kí tự trong `secret` và giảm cho kí tự trong `guess`. Nếu giá trị của kí tự `secret` đang < 0 (tức là đã xuất hiện trong `guess` trước đó), đếm tăng Cow. Tương tự, nếu giá trị của kí tự `guess` đang > 0 (đã xuất hiện trong `secret`), đếm tăng Cow.

**Mã nguồn Java:**
```java
public class Solution {
    public String getHint(String secret, String guess) {
        int bulls = 0;
        int cows = 0;
        int[] numbers = new int[10];
        
        for (int i = 0; i < secret.length(); i++) {
            int s = secret.charAt(i) - '0';
            int g = guess.charAt(i) - '0';
            
            if (s == g) {
                bulls++;
            } else {
                if (numbers[s] < 0) cows++;
                if (numbers[g] > 0) cows++;
                numbers[s]++;
                numbers[g]--;
            }
        }
        
        return bulls + "A" + cows + "B";
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N), duyệt chuỗi một lần.
- Không gian (Space): O(1), mảng tĩnh kích thước 10.

---

## 23. Evaluate Division (Tính toán phép chia đồ thị)

**Đề bài chi tiết:**
Cho một mảng các phương trình (equations) dạng mảng hai phần tử `[A, B]` và mảng giá trị (values) tương ứng đại diện cho `A / B = values[i]`. Cho danh sách các câu truy vấn (queries) dạng `[C, D]`, hãy tính giá trị `C / D`. Nếu không thể xác định kết quả, trả về `-1.0`.

**Phân tích thuật toán:**
Xem các biến A, B là các đỉnh trong một đồ thị có hướng. Mũi tên từ A đến B có trọng số là `values[i]`, và từ B đến A có trọng số là `1.0 / values[i]`.
Đối với mỗi truy vấn `C / D`, ta thực hiện duyệt đồ thị bằng DFS (Tìm kiếm theo chiều sâu) để tìm đường đi từ C đến D. Khi duyệt đồ thị có chu trình, điều cực kì quan trọng là cần một Hash Set (`visited`) để đánh dấu các đỉnh đã duyệt qua, tránh vòng lặp vô tận.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.HashSet;
import java.util.List;
import java.util.Map;
import java.util.Set;

public class Solution {
    public double[] calcEquation(List<List<String>> equations, double[] values, List<List<String>> queries) {
        Map<String, Map<String, Double>> graph = new HashMap<>();
        
        for (int i = 0; i < equations.size(); i++) {
            String u = equations.get(i).get(0);
            String v = equations.get(i).get(1);
            double val = values[i];
            
            graph.putIfAbsent(u, new HashMap<>());
            graph.putIfAbsent(v, new HashMap<>());
            graph.get(u).put(v, val);
            graph.get(v).put(u, 1.0 / val);
        }
        
        double[] results = new double[queries.size()];
        for (int i = 0; i < queries.size(); i++) {
            String start = queries.get(i).get(0);
            String end = queries.get(i).get(1);
            
            if (!graph.containsKey(start) || !graph.containsKey(end)) {
                results[i] = -1.0;
            } else {
                Set<String> visited = new HashSet<>();
                results[i] = dfs(graph, start, end, 1.0, visited);
            }
        }
        return results;
    }
    
    private double dfs(Map<String, Map<String, Double>> graph, String current, String end, double product, Set<String> visited) {
        if (current.equals(end)) return product;
        
        visited.add(current);
        Map<String, Double> neighbors = graph.get(current);
        
        for (Map.Entry<String, Double> neighbor : neighbors.entrySet()) {
            if (!visited.contains(neighbor.getKey())) {
                double result = dfs(graph, neighbor.getKey(), end, product * neighbor.getValue(), visited);
                if (result != -1.0) {
                    return result;
                }
            }
        }
        
        return -1.0;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(Q * (V + E)), với Q là số lượng truy vấn, V và E là số đỉnh và cạnh của đồ thị.
- Không gian (Space): O(V + E) cho đồ thị, và O(V) cho đệ quy cũng như Set `visited`.

---

## 24. Word Ladder (Đổi từ ngắn nhất)

**Đề bài chi tiết:**
Cho hai từ `beginWord`, `endWord` và một danh sách từ vựng `wordList`. Trả về số lượng từ trong chuỗi chuyển đổi ngắn nhất từ `beginWord` đến `endWord`, sao cho mỗi lần chỉ đổi đúng một kí tự và mọi từ trung gian đều phải nằm trong `wordList`. Nếu không có chuỗi nào, trả về 0.

**Phân tích thuật toán:**
Đây là bài toán tìm đường đi ngắn nhất trên đồ thị không trọng số, nên ta dùng BFS.
Để BFS diễn ra cực nhanh, ta đưa toàn bộ `wordList` vào một Hash Set. Mỗi lần muốn biến đổi 1 từ, ta thử đổi lần lượt 1 kí tự của từ đó thành các kí tự từ 'a' đến 'z' và kiểm tra xem từ mới tạo ra có nằm trong Set hay không. Nếu có, nó là trạng thái tiếp theo, đồng thời xóa từ đó khỏi Set để đánh dấu là đã xét qua.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.Set;

public class Solution {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        Set<String> dict = new HashSet<>(wordList);
        if (!dict.contains(endWord)) return 0;
        
        Queue<String> queue = new LinkedList<>();
        queue.offer(beginWord);
        int steps = 1;
        
        while (!queue.isEmpty()) {
            int size = queue.size();
            
            for (int i = 0; i < size; i++) {
                String currentWord = queue.poll();
                if (currentWord.equals(endWord)) return steps;
                
                char[] chars = currentWord.toCharArray();
                for (int j = 0; j < chars.length; j++) {
                    char originalChar = chars[j];
                    for (char c = 'a'; c <= 'z'; c++) {
                        if (c == originalChar) continue;
                        chars[j] = c;
                        String newWord = new String(chars);
                        if (dict.contains(newWord)) {
                            queue.offer(newWord);
                            dict.remove(newWord); // Xóa để không quay lại
                        }
                    }
                    chars[j] = originalChar;
                }
            }
            steps++;
        }
        
        return 0;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(M^2 * N), với M là độ dài mỗi từ, N là số lượng từ trong wordList. Lặp M lần, mỗi lần ghép chuỗi O(M).
- Không gian (Space): O(M * N) để lưu trữ Set và Queue.

---

## 25. Clone Graph (Nhân bản đồ thị)

**Đề bài chi tiết:**
Cho một tham chiếu tới một node trong một đồ thị vô hướng liên thông. Hãy trả về bản sao sâu (deep copy) của đồ thị. (Mỗi node chứa giá trị `val` và một list `neighbors`).

**Phân tích thuật toán:**
Dùng BFS hoặc DFS. Khi duyệt qua đồ thị để tạo node mới, nếu đồ thị có chu trình, ta sẽ gặp lại các node cũ. Ta cần một cơ chế lưu lại ánh xạ từ "Node cũ" sang "Node mới". Cấu trúc Hash Map phù hợp nhất ở đây. Khóa là node cũ, giá trị là node bản sao mới. Tập các khóa trong Hash Map hoạt động y hệt như một HashSet các node `visited`.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

// Định nghĩa Node
class Node {
    public int val;
    public List<Node> neighbors;
    public Node() {
        val = 0;
        neighbors = new ArrayList<Node>();
    }
    public Node(int _val) {
        val = _val;
        neighbors = new ArrayList<Node>();
    }
    public Node(int _val, ArrayList<Node> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
}

public class Solution {
    private Map<Node, Node> visited = new HashMap<>();
    
    public Node cloneGraph(Node node) {
        if (node == null) return null;
        
        // Nếu node này đã được sao chép, trả về bản sao từ Map (hoạt động như Set visited)
        if (visited.containsKey(node)) {
            return visited.get(node);
        }
        
        // Tạo node mới
        Node cloneNode = new Node(node.val, new ArrayList<>());
        visited.put(node, cloneNode);
        
        // Đệ quy sao chép các láng giềng
        for (Node neighbor : node.neighbors) {
            cloneNode.neighbors.add(cloneGraph(neighbor));
        }
        
        return cloneNode;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(V + E), duyệt qua tất cả các đỉnh và cạnh.
- Không gian (Space): O(V), sử dụng Hash Map lưu các đỉnh và Stack (cho DFS đệ quy).

---

## 26. Number of Islands (Đếm số hòn đảo)

**Đề bài chi tiết:**
Cho lưới nhị phân `grid` kích thước `m x n` biểu diễn bản đồ gồm '1' (đất liền) và '0' (nước). Hãy đếm số hòn đảo. Một hòn đảo được bao quanh bởi nước và được hình thành bằng cách nối các vùng đất liền kề theo chiều ngang hoặc dọc.

**Phân tích thuật toán:**
Thông thường bài này được giải bằng cách thay đổi giá trị trong `grid` (biến '1' thành '0' sau khi duyệt qua) hoặc dùng mảng `visited[][]` kiểu boolean. 
Để thực hành cấu trúc Hash Set, ta có thể dùng Set lưu lại các tọa độ đất đã đi qua. Tọa độ được đưa vào Set thông qua một chuỗi đại diện, ví dụ "row,col". Duyệt toàn bộ `grid`, với mỗi ô '1' chưa có trong Set, ta khởi đầu quá trình DFS để duyệt qua toàn bộ hòn đảo và đưa mọi ô của hòn đảo vào Set, sau đó đếm tăng thêm 1.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0) return 0;
        int m = grid.length, n = grid[0].length;
        Set<String> visited = new HashSet<>();
        int islands = 0;
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                String pos = i + "," + j;
                if (grid[i][j] == '1' && !visited.contains(pos)) {
                    dfs(grid, i, j, visited);
                    islands++;
                }
            }
        }
        return islands;
    }
    
    private void dfs(char[][] grid, int i, int j, Set<String> visited) {
        int m = grid.length, n = grid[0].length;
        String pos = i + "," + j;
        
        if (i < 0 || i >= m || j < 0 || j >= n || grid[i][j] == '0' || visited.contains(pos)) {
            return;
        }
        
        visited.add(pos);
        
        dfs(grid, i + 1, j, visited);
        dfs(grid, i - 1, j, visited);
        dfs(grid, i, j + 1, visited);
        dfs(grid, i, j - 1, visited);
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(M * N), duyệt qua mỗi phần tử một lần. Thao tác chuỗi gây tốn thêm hằng số thời gian.
- Không gian (Space): O(M * N) cho HashSet lưu tối đa toàn bộ mảng và đệ quy call stack. (Mảng boolean 2D tối ưu hơn nhiều so với Set kiểu String).

---

## 27. Longest Substring Without Repeating Characters (Chuỗi con không lặp dài nhất)

**Đề bài chi tiết:**
Cho một chuỗi `s`, hãy tìm độ dài chuỗi con dài nhất (substring) không chứa bất kì kí tự nào bị lặp lại.

**Phân tích thuật toán:**
Dùng kỹ thuật Sliding Window (Cửa sổ trượt) cùng Hash Set. Duy trì 2 con trỏ `left` và `right` đại diện cho một cửa sổ. Di chuyển `right` sang phải và thêm kí tự vào Set.
Nếu kí tự tại `right` đã tồn tại trong Set, ta liên tục loại bỏ các kí tự tại `left` khỏi Set và tăng `left` cho tới khi xóa được kí tự lặp đó đi. Cập nhật độ dài lớn nhất sau mỗi bước dịch cửa sổ hợp lệ.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int lengthOfLongestSubstring(String s) {
        Set<Character> window = new HashSet<>();
        int left = 0, right = 0, maxLength = 0;
        
        while (right < s.length()) {
            char c = s.charAt(right);
            if (!window.contains(c)) {
                window.add(c);
                maxLength = Math.max(maxLength, right - left + 1);
                right++;
            } else {
                window.remove(s.charAt(left));
                left++;
            }
        }
        
        return maxLength;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N), trong trường hợp xấu nhất, mỗi kí tự bị duyệt qua tối đa 2 lần (1 bởi `right`, 1 bởi `left`).
- Không gian (Space): O(min(M, N)), giới hạn bởi kích thước bảng chữ cái `M`, Hash Set chứa tối đa `M` kí tự.

---

## 28. Find the Duplicate Number (Tìm số lặp lại)

**Đề bài chi tiết:**
Cho một mảng các số nguyên `nums` có độ dài `n + 1`, chứa các số nguyên nằm trong khoảng từ `1` đến `n`. Hãy tìm số lặp lại duy nhất đó mà không sửa đổi mảng và sử dụng bộ nhớ bổ sung giới hạn là O(1).
(Dù vậy, ta sẽ thực hành cách dùng Hash Set ở bài giải này).

**Phân tích thuật toán:**
Duyệt qua mảng và cố gắng thêm từng số vào một Hash Set. Hàm `add()` của HashSet trả về `false` nếu số đó đã tồn tại trong tập hợp. Khi gặp `false`, phần tử đó chính là số lặp lại.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int findDuplicate(int[] nums) {
        Set<Integer> seen = new HashSet<>();
        for (int num : nums) {
            if (!seen.add(num)) {
                return num;
            }
        }
        return -1;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N), duyệt một lần qua mảng.
- Không gian (Space): O(N), lưu trữ các số đã xét trong Hash Set. Yêu cầu O(1) space của đề bài có thể được giải quyết bằng thuật toán Floyd's Tortoise and Hare (Con trỏ Nhanh Chậm).

---

## 29. Find All Duplicates in an Array (Tìm tất cả số trùng)

**Đề bài chi tiết:**
Cho mảng số nguyên `nums` độ dài `n`, trong đó các số nguyên nằm trong khoảng `[1, n]`. Mỗi số nguyên xuất hiện một lần hoặc hai lần. Trả về mảng chứa tất cả các số nguyên xuất hiện hai lần.
Bạn phải viết một thuật toán chạy trong O(n) và sử dụng thêm không gian O(1) (bỏ qua không gian cho mảng trả về). (Ở đây ta sử dụng Hash Set).

**Phân tích thuật toán:**
Tương tự bài 28, ta đưa các số trong mảng vào Hash Set. Nếu `add()` thất bại, ta thêm phần tử đó vào danh sách kết quả.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

public class Solution {
    public List<Integer> findDuplicates(int[] nums) {
        List<Integer> duplicates = new ArrayList<>();
        Set<Integer> seen = new HashSet<>();
        
        for (int num : nums) {
            if (!seen.add(num)) {
                duplicates.add(num);
            }
        }
        
        return duplicates;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N), duyệt mảng một lần.
- Không gian (Space): O(N), lưu các số vào Hash Set. Yêu cầu O(1) có thể thực hiện bằng cách đảo dấu phần tử mảng tại chỉ số tương ứng.

---

## 30. Maximum Number of Balloons (Số lượng chữ "balloon")

**Đề bài chi tiết:**
Cho chuỗi `text`, hãy đếm xem bạn có thể tạo thành bao nhiêu từ "balloon" từ các kí tự của `text`. Bạn có thể sử dụng mỗi kí tự trong `text` tối đa một lần.

**Phân tích thuật toán:**
Thay vì chỉ dùng Set, ta cần đếm tần suất xuất hiện. Chữ "balloon" yêu cầu `b: 1, a: 1, l: 2, o: 2, n: 1`. 
Đếm số lần xuất hiện của 5 kí tự này trong chuỗi `text`. Để tìm xem tạo được bao nhiêu từ, ta chia số lượng 'l' và 'o' cho 2, sau đó tìm giá trị nhỏ nhất trong số tần suất của 5 kí tự này.

**Mã nguồn Java:**
```java
public class Solution {
    public int maxNumberOfBalloons(String text) {
        int[] counts = new int[26];
        for (char c : text.toCharArray()) {
            counts[c - 'a']++;
        }
        
        int b = counts['b' - 'a'];
        int a = counts['a' - 'a'];
        int l = counts['l' - 'a'] / 2;
        int o = counts['o' - 'a'] / 2;
        int n = counts['n' - 'a'];
        
        return Math.min(b, Math.min(a, Math.min(l, Math.min(o, n))));
    }
}
```

**Độ phức tạp:**
- Thời gian (Time): O(N), trong đó N là độ dài chuỗi `text`.
- Không gian (Space): O(1), mảng tần suất cố định có kích thước 26.

---
