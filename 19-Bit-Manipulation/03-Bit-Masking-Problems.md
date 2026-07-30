# 03 - Bit Masking Problems

Tài liệu này bao gồm 30 bài tập ứng dụng kỹ thuật Bit Masking (Mặt nạ bit). 10 bài đầu tiên cung cấp đầy đủ đề bài, phân tích, mã nguồn Java và độ phức tạp. Các bài từ 11 đến 30 được tóm tắt.

## 1. Subsets (Bitmask approach)

**Đề bài chi tiết:**
Cho một mảng các số nguyên duy nhất `nums`. Trả về tất cả các tập con (subsets) có thể có (tập lũy thừa - power set). Giải pháp không được chứa các tập con trùng lặp.

**Phân tích thuật toán:**
Một mảng có $N$ phần tử sẽ có chính xác $2^N$ tập con. Ta có thể biểu diễn mỗi tập con dưới dạng một con số nguyên từ $0$ đến $2^N - 1$. Với mỗi số nguyên, ta kiểm tra các bit của nó; nếu bit thứ $j$ là 1 (tức là `(i & (1 << j)) != 0`), ta đưa phần tử `nums[j]` vào tập con hiện tại.
Đây là cách thuần túy nhất để ánh xạ từ bitmask sang cấu hình phần tử (tổ hợp).

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class Subsets {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        int n = nums.length;
        int totalSubsets = 1 << n; // 2^n
        
        for (int mask = 0; mask < totalSubsets; mask++) {
            List<Integer> currentSubset = new ArrayList<>();
            for (int i = 0; i < n; i++) {
                // Kiểm tra xem bit thứ i của mask có bật (bằng 1) không
                if ((mask & (1 << i)) != 0) {
                    currentSubset.add(nums[i]);
                }
            }
            result.add(currentSubset);
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N \cdot 2^N)$. Có $2^N$ vòng lặp ngoài, mỗi vòng có $N$ thao tác dịch bit bên trong.
- **Space Complexity:** $O(N \cdot 2^N)$ để lưu trữ toàn bộ các tập con sinh ra trả về kết quả. Không gian phụ là $O(N)$ cho `currentSubset`.

---

## 2. Maximum Product of Word Lengths

**Đề bài chi tiết:**
Cho mảng chuỗi `words`. Hãy trả về giá trị lớn nhất của `length(words[i]) * length(words[j])` trong đó hai chuỗi không dùng chung bất kỳ ký tự nào. Nếu không tồn tại cặp chuỗi nào như vậy, trả về 0. Mỗi chuỗi chỉ chứa chữ cái tiếng Anh in thường.

**Phân tích thuật toán:**
Do các chuỗi chỉ chứa chữ cái từ 'a' đến 'z' (26 ký tự), ta có thể mã hóa (compress state) mỗi chuỗi thành một bitmask 32-bit nguyên. Bit 0 tương ứng với chữ 'a', bit 1 tương ứng với chữ 'b', v.v...
Sau khi chuyển đổi tất cả chuỗi sang mảng bitmask, ta xét tất cả các cặp $i$ và $j$. Hai chuỗi không có ký tự chung khi và chỉ khi `masks[i] & masks[j] == 0`.

**Mã nguồn Java:**
```java
public class MaxProductWordLengths {
    public int maxProduct(String[] words) {
        int n = words.length;
        int[] masks = new int[n];
        
        for (int i = 0; i < n; i++) {
            for (char c : words[i].toCharArray()) {
                masks[i] |= (1 << (c - 'a')); // Bật bit tương ứng với ký tự
            }
        }
        
        int maxProduct = 0;
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                // Nếu bitwise AND bằng 0, nghĩa là không có ký tự chung
                if ((masks[i] & masks[j]) == 0) {
                    maxProduct = Math.max(maxProduct, words[i].length() * words[j].length());
                }
            }
        }
        return maxProduct;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N \cdot L + N^2)$ với $N$ là số lượng chuỗi, $L$ là độ dài trung bình của chuỗi. Mã hóa tốn $O(N \cdot L)$, so sánh cặp tốn $O(N^2)$.
- **Space Complexity:** $O(N)$ để lưu mảng `masks`.

---

## 3. Maximum Length of a Concatenated String with Unique Characters

**Đề bài chi tiết:**
Cho một mảng chuỗi `arr`. Nối một mảng con (có thể rỗng) của `arr` lại với nhau để tạo ra chuỗi $S$. Hãy tìm độ dài dài nhất của $S$ thỏa mãn $S$ chỉ chứa các ký tự duy nhất (unique characters).

**Phân tích thuật toán:**
Bài toán tương đương với bài toán cái túi (Knapsack), có thể giải bằng đệ quy (Backtracking). Để kiểm tra xem chuỗi mới nối thêm có trùng lặp ký tự với chuỗi hiện tại không, ta dùng bitmask cho từng chuỗi (mỗi chuỗi là 1 số nguyên, các bit từ 0 đến 25).
Trước hết loại bỏ các chuỗi tự nó chứa ký tự trùng nhau.
Trong hàm đệ quy, duy trì trạng thái của tập hợp ký tự đã chọn thông qua một bitmask. Nếu `currentMask & newStringMask != 0`, nghĩa là có xung đột, bỏ qua; nếu không, cập nhật mask `currentMask | newStringMask`.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class MaxLengthUniqueString {
    public int maxLength(List<String> arr) {
        List<Integer> masks = new ArrayList<>();
        List<Integer> lens = new ArrayList<>();
        
        for (String s : arr) {
            int mask = 0;
            boolean valid = true;
            for (char c : s.toCharArray()) {
                int bit = c - 'a';
                if ((mask & (1 << bit)) != 0) {
                    valid = false;
                    break;
                }
                mask |= (1 << bit);
            }
            if (valid) {
                masks.add(mask);
                lens.add(s.length());
            }
        }
        
        return backtrack(masks, lens, 0, 0);
    }
    
    private int backtrack(List<Integer> masks, List<Integer> lens, int index, int currentMask) {
        if (index == masks.size()) {
            return 0;
        }
        
        // Không chọn chuỗi hiện tại
        int exclude = backtrack(masks, lens, index + 1, currentMask);
        
        // Chọn chuỗi hiện tại (nếu hợp lệ)
        int include = 0;
        if ((currentMask & masks.get(index)) == 0) {
            include = lens.get(index) + backtrack(masks, lens, index + 1, currentMask | masks.get(index));
        }
        
        return Math.max(exclude, include);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(2^N)$ với $N$ là kích thước danh sách `arr` (tối đa 16 phần tử).
- **Space Complexity:** $O(N)$ cho call stack đệ quy và mảng lưu `masks`.

---

## 4. Generalized Abbreviation

**Đề bài chi tiết:**
Viết một hàm sinh tất cả các cách viết tắt (abbreviation) tổng quát của một chuỗi. Một cách viết tắt được thực hiện bằng cách thay thế bất kỳ số lượng ký tự không liền kề bằng số lượng ký tự bị thay thế.
VD: `word`, các kết quả gồm: `["word", "1ord", "w1rd", "wo1d", "wor1", "2rd", "w2d", "wo2", "1o1d", "1or1", "w1r1", "1o2", "2r1", "3d", "w3", "4"]`.

**Phân tích thuật toán:**
Mỗi ký tự của từ có thể bị "giữ lại" hoặc "thay thế bằng số". Có $N$ ký tự thì có $2^N$ trạng thái, hoàn toàn biểu diễn bằng bitmask. Nếu bit thứ $i$ của mask là 1, ta biến ký tự $i$ thành số. Những bit 1 đứng liền kề sẽ được đếm tổng để gom lại thành một số. Bit 0 giữ nguyên ký tự.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class GeneralizedAbbreviation {
    public List<String> generateAbbreviations(String word) {
        List<String> res = new ArrayList<>();
        int n = word.length();
        int total = 1 << n; // 2^n masks
        
        for (int mask = 0; mask < total; mask++) {
            StringBuilder sb = new StringBuilder();
            int count = 0;
            
            for (int i = 0; i < n; i++) {
                // Kiểm tra bit từ trái qua phải hoặc phải qua trái đều được
                // Dưới đây duyệt từ bit cao nhất (trái) tới thấp (phải) nếu i tăng
                // Chúng ta gán bit 1 là bị rút gọn
                if ((mask & (1 << (n - 1 - i))) != 0) { 
                    count++;
                } else {
                    if (count > 0) {
                        sb.append(count);
                        count = 0;
                    }
                    sb.append(word.charAt(i));
                }
            }
            if (count > 0) {
                sb.append(count);
            }
            res.add(sb.toString());
        }
        
        return res;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N \cdot 2^N)$. Duyệt qua tất cả $2^N$ mask, với mỗi mask xử lý chuỗi $O(N)$.
- **Space Complexity:** $O(N \cdot 2^N)$ để lưu mảng đáp án.

---

## 5. Binary Watch

**Đề bài chi tiết:**
Một chiếc đồng hồ nhị phân (Binary Watch) có 4 đèn LED ở trên đại diện cho giờ (0-11) và 6 đèn LED ở dưới đại diện cho phút (0-59). Cho số lượng đèn đang sáng là `turnedOn`, trả về tất cả các thời gian hợp lệ có thể hiển thị. (Giờ không có số 0 ở đầu, phút luôn có 2 chữ số).

**Phân tích thuật toán:**
Chiếc đồng hồ có 10 đèn LED tương đương với một số nguyên 10-bit (từ $0$ đến $2^{10}-1 = 1023$). 4 bit cao đại diện cho giờ, 6 bit thấp cho phút. Ta có thể duyệt toàn bộ 1024 trạng thái, đếm số lượng bit 1. Nếu bằng `turnedOn`, ta tách giờ và phút ra, kiểm tra tính hợp lệ (`hour < 12`, `minute < 60`), rồi định dạng chuỗi kết quả.
Một cách khác không dùng mask mảng 10 bit là duyệt `h` từ 0..11 và `m` từ 0..59, rồi dùng `Integer.bitCount(h) + Integer.bitCount(m) == turnedOn`. Cách này cũng là ứng dụng Bitmask!

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class BinaryWatch {
    public List<String> readBinaryWatch(int turnedOn) {
        List<String> result = new ArrayList<>();
        
        for (int h = 0; h < 12; h++) {
            for (int m = 0; m < 60; m++) {
                if (Integer.bitCount(h) + Integer.bitCount(m) == turnedOn) {
                    result.add(String.format("%d:%02d", h, m));
                }
            }
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(1)$ do hằng số lặp $12 \times 60 = 720$.
- **Space Complexity:** $O(1)$ để xử lý, bỏ qua mảng kết quả trả về.

---

## 6. Letter Case Permutation (Bitmask view)

**Đề bài chi tiết:**
Cho một chuỗi `s`, ta có thể đổi bất kỳ chữ cái nào thành chữ hoa hoặc chữ thường để tạo ra chuỗi mới. Trả về tất cả các chuỗi có thể tạo thành.

**Phân tích thuật toán:**
Đầu tiên đếm xem có bao nhiêu chữ cái trong chuỗi `s` (gọi là $M$). Có $M$ chữ cái thì sẽ có $2^M$ cấu hình hoán vị hoa/thường. Dùng bitmask từ $0$ đến $2^M - 1$. Với một cấu hình mask, khi duyệt lại chuỗi `s`, cứ hễ gặp một chữ cái (cái thứ $j$), ta kiểm tra bit thứ $j$ của mask. Nếu là 0, để chữ thường, nếu 1, biến thành chữ hoa.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class LetterCasePermutation {
    public List<String> letterCasePermutation(String s) {
        List<String> res = new ArrayList<>();
        
        // Đếm số chữ cái
        int m = 0;
        for (char c : s.toCharArray()) {
            if (Character.isLetter(c)) m++;
        }
        
        int total = 1 << m; // 2^m cấu hình
        for (int mask = 0; mask < total; mask++) {
            StringBuilder sb = new StringBuilder();
            int bitIndex = 0;
            for (char c : s.toCharArray()) {
                if (Character.isLetter(c)) {
                    if ((mask & (1 << bitIndex)) != 0) {
                        sb.append(Character.toUpperCase(c));
                    } else {
                        sb.append(Character.toLowerCase(c));
                    }
                    bitIndex++;
                } else {
                    sb.append(c);
                }
            }
            res.add(sb.toString());
        }
        
        return res;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N \cdot 2^M)$ với $N$ là độ dài chuỗi $S$, $M$ là số lượng chữ cái.
- **Space Complexity:** $O(N \cdot 2^M)$ cho mảng lưu kết quả.

---

## 7. Check If a String Contains All Binary Codes of Size K

**Đề bài chi tiết:**
Cho chuỗi nhị phân `s` và số nguyên `k`. Kiểm tra xem mọi mã nhị phân có độ dài `k` đều là chuỗi con (substring) của `s` hay không. Trả về `true` hoặc `false`.

**Phân tích thuật toán:**
Số lượng mã nhị phân độ dài `k` là $2^k$. Do `k` nhỏ (max = 20), ta có thể duyệt cửa sổ trượt độ dài `k` trên `s`. Thay vì lưu bằng Set chuỗi, ta lưu dưới dạng số nguyên (từ giá trị bitmask của chuỗi).
Bắt đầu với một sliding window, mỗi lần dịch phải một ký tự, ta loại bỏ bit cao nhất của mask cũ (dùng `& ~(1 << k)`) và thêm bit mới vào vị trí thấp nhất (dịch trái, rồi OR với `0` hoặc `1`). Đẩy số nguyên sinh ra vào mảng/set Boolean, sau đó đếm số lượng số đã gặp xem có đủ $2^k$ không.

**Mã nguồn Java:**
```java
public class StringContainsBinaryCodes {
    public boolean hasAllCodes(String s, int k) {
        int targetCount = 1 << k;
        if (s.length() < targetCount + k - 1) {
            return false;
        }
        
        boolean[] seen = new boolean[targetCount];
        int count = 0;
        int mask = 0;
        int allOnes = targetCount - 1; // Tạo mặt nạ k bit 1
        
        for (int i = 0; i < s.length(); i++) {
            // Dịch trái và thêm bit cuối
            mask = ((mask << 1) & allOnes) | (s.charAt(i) - '0');
            
            // Cửa sổ đủ kích thước
            if (i >= k - 1) {
                if (!seen[mask]) {
                    seen[mask] = true;
                    count++;
                    if (count == targetCount) {
                        return true;
                    }
                }
            }
        }
        return false;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ với $N$ là độ dài chuỗi `s`.
- **Space Complexity:** $O(2^K)$ cho mảng boolean `seen`.

---

## 8. Maximum Score Words Formed by Letters

**Đề bài chi tiết:**
Cho một danh sách các từ `words`, mảng ký tự `letters`, và điểm số của mọi chữ cái `score` (từ a-z). Trả về tổng điểm cao nhất của các từ có thể tạo thành bằng cách dùng những chữ cái có trong `letters`. (Các từ có thể bỏ qua).

**Phân tích thuật toán:**
Bởi vì danh sách `words` có độ dài tối đa 14 (rất nhỏ), ta có thể vét cạn (backtracking hoặc bitmask). Dùng bitmask từ $0$ tới $2^N-1$ để biểu diễn tập hợp các từ `words` được chọn. Với mỗi tập hợp, ta đếm xem tổng tần suất các ký tự cần dùng có vượt quá số ký tự có sẵn trong `letters` không. Nếu không, ta tính tổng điểm và cập nhật `maxScore`.

**Mã nguồn Java:**
```java
public class MaxScoreWords {
    public int maxScoreWords(String[] words, char[] letters, int[] score) {
        int[] count = new int[26];
        for (char c : letters) {
            count[c - 'a']++;
        }
        
        int n = words.length;
        int maxTotalScore = 0;
        
        for (int mask = 0; mask < (1 << n); mask++) {
            int[] currentCount = new int[26];
            int currentScore = 0;
            boolean isValid = true;
            
            for (int i = 0; i < n; i++) {
                if ((mask & (1 << i)) != 0) {
                    for (char c : words[i].toCharArray()) {
                        int index = c - 'a';
                        currentCount[index]++;
                        currentScore += score[index];
                        if (currentCount[index] > count[index]) {
                            isValid = false;
                        }
                    }
                }
            }
            if (isValid) {
                maxTotalScore = Math.max(maxTotalScore, currentScore);
            }
        }
        
        return maxTotalScore;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(2^N \cdot L)$ với $N \le 14$ số từ, $L$ tổng độ dài chuỗi tạo thành. $2^{14} \approx 16384$, rất nhanh.
- **Space Complexity:** $O(1)$ mảng count cố định 26 kí tự.

---

## 9. Partition to K Equal Sum Subsets (Bitmask DP preview)

**Đề bài chi tiết:**
Cho mảng số nguyên dương `nums` và số `k`, hãy kiểm tra xem mảng có thể chia thành `k` tập con không giao nhau mà tổng các phần tử trong mỗi tập con bằng nhau không.

**Phân tích thuật toán:**
Có thể giải bằng Backtracking. Nhưng một cách tốt hơn để giảm nhánh gọi thừa là Bitmask DP kết hợp Memoization. Ở trạng thái bitmask, bit 1 là chưa chọn, bit 0 là đã chọn (hoặc ngược lại). Ta duyệt toàn bộ mảng `nums` xem phần tử nào có thể đưa vào subset hiện tại (vẫn đang nhỏ hơn `target_sum = sum / k`). Khi tổng của subset đầy, ta đặt lại và bắt đầu gom subset tiếp theo.

**Mã nguồn Java (DP Bitmask & Memoization):**
```java
import java.util.Arrays;

public class PartitionKSubsets {
    public boolean canPartitionKSubsets(int[] nums, int k) {
        int sum = 0;
        for (int num : nums) sum += num;
        if (sum % k != 0) return false;
        
        int target = sum / k;
        int n = nums.length;
        // memo[mask] = true nếu từ mask này có thể chia được, ngược lại false
        Boolean[] memo = new Boolean[1 << n];
        Arrays.sort(nums); 
        return dfs(nums, (1 << n) - 1, 0, target, k, memo);
    }
    
    private boolean dfs(int[] nums, int mask, int currentSum, int target, int k, Boolean[] memo) {
        if (k == 0) return true; // Hoàn thành
        
        if (memo[mask] != null) return memo[mask];
        
        if (currentSum == target) {
            // Subset đầy, sang subset tiếp theo, giữ nguyên mask hiện hành
            boolean res = dfs(nums, mask, 0, target, k - 1, memo);
            memo[mask] = res;
            return res;
        }
        
        for (int i = 0; i < nums.length; i++) {
            // Nếu bit i bằng 1 (chưa sử dụng) và không làm vượt quá target
            if ((mask & (1 << i)) != 0 && currentSum + nums[i] <= target) {
                // Tắt bit i
                int nextMask = mask ^ (1 << i);
                if (dfs(nums, nextMask, currentSum + nums[i], target, k, memo)) {
                    return memo[mask] = true;
                }
            }
        }
        
        return memo[mask] = false;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N \cdot 2^N)$. Dùng memoization giới hạn số trạng thái tối đa là $2^N$.
- **Space Complexity:** $O(2^N)$ bộ nhớ cho mảng `memo`.

---

## 10. Matchsticks to Square

**Đề bài chi tiết:**
Cho mảng số nguyên dương `matchsticks` đại diện cho độ dài các que diêm. Không được bẻ gãy que diêm nào nhưng có thể nối tiếp chúng. Hãy kiểm tra xem có thể dùng hết tất cả que diêm để ghép thành một hình vuông không.

**Phân tích thuật toán:**
Đây thực chất là trường hợp đặc biệt của bài toán "Partition to K Equal Sum Subsets" với `k = 4`. Tổng độ dài các que diêm phải chia hết cho 4. Các que diêm có độ dài lớn hơn `sum/4` sẽ làm bài toán không có giải pháp ngay. Thuật toán có thể dùng y hệt Bài 9 bằng cách dùng Bitmask DP hoặc Backtracking tối ưu.

**Mã nguồn Java:**
```java
import java.util.Arrays;

public class MatchsticksSquare {
    public boolean makesquare(int[] matchsticks) {
        int sum = 0;
        for (int m : matchsticks) sum += m;
        if (sum % 4 != 0) return false;
        
        int target = sum / 4;
        int n = matchsticks.length;
        Boolean[] memo = new Boolean[1 << n];
        
        // Tối ưu hóa: sắp xếp que diêm giảm dần
        Arrays.sort(matchsticks);
        reverse(matchsticks);
        
        return dfs(matchsticks, (1 << n) - 1, 0, target, 4, memo);
    }
    
    private boolean dfs(int[] nums, int mask, int currentSum, int target, int k, Boolean[] memo) {
        if (k == 0) return true;
        if (memo[mask] != null) return memo[mask];
        
        if (currentSum == target) {
            boolean res = dfs(nums, mask, 0, target, k - 1, memo);
            return memo[mask] = res;
        }
        
        for (int i = 0; i < nums.length; i++) {
            if ((mask & (1 << i)) != 0 && currentSum + nums[i] <= target) {
                if (dfs(nums, mask ^ (1 << i), currentSum + nums[i], target, k, memo)) {
                    return memo[mask] = true;
                }
                // Nếu không tạo thành thì có thể bỏ qua nhánh trùng lặp (không cài đặt ở đây)
            }
        }
        return memo[mask] = false;
    }
    
    private void reverse(int[] nums) {
        int i = 0, j = nums.length - 1;
        while (i < j) {
            int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
            i++; j--;
        }
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N \cdot 2^N)$. Trạng thái DP $2^N$.
- **Space Complexity:** $O(2^N)$ cho bảng memo array.

---

## 11. Find The Difference

**Đề bài chi tiết:**
Cho hai chuỗi `s` và `t`. Chuỗi `t` được tạo ra bằng cách xáo trộn ngẫu nhiên các ký tự của chuỗi `s` và sau đó chèn thêm một chữ cái nữa ở một vị trí bất kỳ. Tìm chữ cái đã được thêm vào `t`.

**Phân tích thuật toán:**
Bài toán có thể giải quyết gọn nhẹ bằng phép toán XOR. Tính chất cơ bản của XOR là `a ^ a = 0` và `a ^ 0 = a`. Vì `t` có đúng một ký tự nhiều hơn `s`, và mọi ký tự khác đều xuất hiện theo cặp (một trong `s`, một trong `t`), khi ta XOR giá trị ASCII của tất cả các ký tự trong cả hai chuỗi, các ký tự giống nhau sẽ tự triệt tiêu thành 0, kết quả cuối cùng sẽ chính là ký tự được thêm vào.

**Mã nguồn Java:**
```java
public class FindTheDifference {
    public char findTheDifference(String s, String t) {
        char result = 0;
        
        // XOR tất cả ký tự trong s
        for (char c : s.toCharArray()) {
            result ^= c;
        }
        
        // XOR tất cả ký tự trong t
        for (char c : t.toCharArray()) {
            result ^= c;
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ với $N$ là độ dài chuỗi `s`, do duyệt qua cả hai chuỗi `s` và `t` một lần.
- **Space Complexity:** $O(1)$, chỉ dùng biến `result` để lưu trữ phép toán XOR.

---

## 12. Single Number I, II, III

**Đề bài chi tiết:**
Ba bài toán kinh điển về tìm số duy nhất trong mảng:
1. **Single Number I**: Mỗi phần tử trong mảng xuất hiện đúng 2 lần, chỉ có một phần tử xuất hiện 1 lần. Tìm phần tử đó.
2. **Single Number II**: Mỗi phần tử trong mảng xuất hiện đúng 3 lần, chỉ có một phần tử xuất hiện 1 lần. Tìm phần tử đó.
3. **Single Number III**: Mỗi phần tử trong mảng xuất hiện đúng 2 lần, chỉ có hai phần tử xuất hiện 1 lần. Tìm hai phần tử đó.

**Phân tích thuật toán:**
- **Bài I**: Sử dụng tính chất `a ^ a = 0`. XOR toàn bộ mảng sẽ triệt tiêu các phần tử trùng lặp và giữ lại phần tử duy nhất.
- **Bài II**: Dùng trạng thái để theo dõi các bit 1. Biến `ones` lưu các bit 1 xuất hiện 1 lần (modulo 3), biến `twos` lưu các bit 1 xuất hiện 2 lần. Khi một bit 1 xuất hiện 3 lần, nó bị xóa khỏi cả `ones` và `twos`.
- **Bài III**: XOR toàn bộ mảng sẽ trả về kết quả `x ^ y` (với `x` và `y` là 2 phần tử xuất hiện 1 lần). Vì `x != y`, nên `x ^ y` có ít nhất 1 bit bật (bằng 1). Lấy ra bit bật phải nhất bằng `diff &= -diff` để chia mảng thành 2 nhóm (nhóm có bit đó bật và nhóm không có). XOR riêng từng nhóm ta sẽ tìm được `x` và `y`.

**Mã nguồn Java:**
```java
public class SingleNumbers {
    // Single Number I
    public int singleNumberI(int[] nums) {
        int result = 0;
        for (int num : nums) {
            result ^= num;
        }
        return result;
    }

    // Single Number II
    public int singleNumberII(int[] nums) {
        int ones = 0, twos = 0;
        for (int num : nums) {
            ones = (ones ^ num) & ~twos;
            twos = (twos ^ num) & ~ones;
        }
        return ones;
    }

    // Single Number III
    public int[] singleNumberIII(int[] nums) {
        int xor = 0;
        for (int num : nums) {
            xor ^= num;
        }
        
        // Lấy bit 1 tận cùng bên phải
        int diff = xor & (-xor); 
        int[] result = new int[2];
        for (int num : nums) {
            if ((num & diff) == 0) {
                result[0] ^= num;
            } else {
                result[1] ^= num;
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ cho cả ba bài toán vì chỉ cần quét qua mảng từ 1 đến 2 lần.
- **Space Complexity:** $O(1)$ chỉ tốn bộ nhớ cho vài biến nguyên.

---

## 13. Missing Number

**Đề bài chi tiết:**
Cho một mảng chứa $n$ số nguyên phân biệt trong khoảng từ $0$ đến $n$. Hãy tìm số duy nhất trong khoảng này bị thiếu trong mảng. Yêu cầu giải thuật có độ phức tạp thời gian tuyến tính và chỉ dùng bộ nhớ không gian $O(1)$.

**Phân tích thuật toán:**
Một cách để giải là tính tổng từ $0$ đến $n$ rồi trừ đi tổng các phần tử trong mảng, nhưng điều này dễ dẫn đến tràn số (Integer Overflow) nếu $n$ rất lớn.
Sử dụng XOR là giải pháp an toàn hơn: ta có thể XOR tất cả các số từ $0$ đến $n$, đồng thời XOR tất cả các phần tử trong mảng. Các phần tử xuất hiện trong mảng sẽ tự triệt tiêu với giá trị chỉ số tương ứng trong khoảng $0..n$. Số còn lại chính là phần tử bị thiếu.

**Mã nguồn Java:**
```java
public class MissingNumber {
    public int missingNumber(int[] nums) {
        int missing = nums.length;
        for (int i = 0; i < nums.length; i++) {
            // XOR với biến đếm vòng lặp và phần tử tương ứng của mảng
            missing ^= i ^ nums[i];
        }
        return missing;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$, duyệt qua mảng 1 lần.
- **Space Complexity:** $O(1)$ chỉ sử dụng thêm một biến `missing`.

---

## 14. Counting Bits

**Đề bài chi tiết:**
Cho số nguyên `n`, hãy trả về một mảng `ans` có độ dài `n + 1` sao cho với mỗi số $i$ ($0 \le i \le n$), `ans[i]` là số lượng bit 1 có trong biểu diễn nhị phân của $i$.

**Phân tích thuật toán:**
Để tính nhanh mà không cần lặp lại phép đếm cho từng phần tử (phương pháp $O(N \log N)$), ta dùng quy hoạch động (DP) kết hợp thao tác bit. Số lượng bit 1 của số $i$ chính bằng số lượng bit 1 của $i/2$ (tức là $i$ dịch phải 1 bit) cộng thêm bit tận cùng bên phải của $i$ (bằng 1 nếu lẻ, 0 nếu chẵn). 
Công thức truy hồi: `dp[i] = dp[i >> 1] + (i & 1)`.

**Mã nguồn Java:**
```java
public class CountingBits {
    public int[] countBits(int n) {
        int[] dp = new int[n + 1];
        dp[0] = 0;
        
        for (int i = 1; i <= n; i++) {
            // dp[i >> 1] tái sử dụng kết quả đã tính trước đó
            dp[i] = dp[i >> 1] + (i & 1);
        }
        
        return dp;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ vì mỗi phần tử chỉ cần tính qua 1 bước hằng số.
- **Space Complexity:** $O(N)$ để lưu trữ mảng kết quả `ans`.

---

## 15. Hamming Distance

**Đề bài chi tiết:**
Khoảng cách Hamming giữa hai số nguyên là số vị trí mà tại đó các bit tương ứng của chúng khác nhau. Cho hai số nguyên `x` và `y`, hãy tính khoảng cách Hamming giữa chúng.

**Phân tích thuật toán:**
Để xác định các vị trí bit khác biệt giữa hai số, phép toán XOR là giải pháp tối ưu. Kết quả của `x ^ y` sẽ là một số mà bit bằng 1 tại các vị trí `x` và `y` khác nhau. Việc còn lại là đếm số lượng bit 1 của kết quả XOR này (có thể dùng thuật toán Brian Kernighan hoặc có sẵn hàm hỗ trợ của Java).

**Mã nguồn Java:**
```java
public class HammingDistanceClass {
    public int hammingDistance(int x, int y) {
        int xorResult = x ^ y;
        int count = 0;
        
        // Đếm số lượng bit 1 của xorResult
        while (xorResult != 0) {
            count += xorResult & 1;
            xorResult >>>= 1; // Sử dụng unsigned right shift
        }
        
        return count;
        // Hoặc có thể dùng gọn hơn: return Integer.bitCount(x ^ y);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(1)$ vì số lần lặp lớn nhất bằng số bit của cấu trúc kiểu nguyên (32 bit).
- **Space Complexity:** $O(1)$.

---

## 16. Total Hamming Distance

**Đề bài chi tiết:**
Cho mảng số nguyên `nums`. Hãy tính tổng khoảng cách Hamming giữa tất cả các cặp số nguyên trong mảng.

**Phân tích thuật toán:**
Nếu tính khoảng cách của từng cặp phần tử một, độ phức tạp sẽ là $O(N^2)$ (rất chậm đối với mảng lớn). Thay vào đó, ta đổi quan điểm, xét độc lập từng bit từ cột $0$ đến $31$.
Tại một vị trí bit thứ $i$ bất kỳ: giả sử trong $N$ số có $k$ số mang bit 1, thì số lượng số mang bit 0 sẽ là $N - k$. Tổng số cặp có bit khác biệt tại cột đó sẽ là $k \times (N - k)$. Ta tính tổng đại lượng này trên tất cả 32 vị trí bit.

**Mã nguồn Java:**
```java
public class TotalHammingDistanceClass {
    public int totalHammingDistance(int[] nums) {
        int total = 0;
        int n = nums.length;
        
        // Duyệt qua 32 cột bit
        for (int i = 0; i < 32; i++) {
            int bitCount = 0; // Số lượng bit 1 tại cột i
            
            for (int num : nums) {
                bitCount += (num >> i) & 1;
            }
            
            // Số lượng cặp khác biệt tại cột i
            total += bitCount * (n - bitCount);
        }
        
        return total;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(32 \cdot N) = O(N)$. Ta lặp qua toàn bộ mảng đúng 32 lần.
- **Space Complexity:** $O(1)$ chỉ sử dụng vài biến lưu trữ phụ.

---

## 17. Power of Two, Power of Four

**Đề bài chi tiết:**
- **Power of Two**: Cho số nguyên `n`, kiểm tra xem nó có phải là lũy thừa của 2 hay không.
- **Power of Four**: Cho số nguyên `n`, kiểm tra xem nó có phải là lũy thừa của 4 hay không.

**Phân tích thuật toán:**
- **Power of 2**: Một số nguyên dương là lũy thừa của 2 khi và chỉ khi nó có đúng 1 bit `1` trong biểu diễn nhị phân. Điều này tương đương với `n > 0` và phép toán `n & (n - 1) == 0`.
- **Power of 4**: Một số là lũy thừa của 4 thì hiển nhiên phải là lũy thừa của 2. Thêm vào đó, bit `1` duy nhất của nó phải nằm ở vị trí chẵn (vị trí 0, 2, 4,...). Ta kiểm tra bằng mask xen kẽ các bit chẵn/lẻ là `0x55555555` (biểu diễn nhị phân `...01010101`). Nếu bit 1 của `n` nằm trên vị trí chẵn, phép AND với `0x55555555` sẽ khác 0.

**Mã nguồn Java:**
```java
public class PowerOfNumbers {
    public boolean isPowerOfTwo(int n) {
        return n > 0 && (n & (n - 1)) == 0;
    }

    public boolean isPowerOfFour(int n) {
        // Điều kiện 1: n > 0
        // Điều kiện 2: là lũy thừa của 2
        // Điều kiện 3: bit 1 nằm ở vị trí chẵn
        return n > 0 && (n & (n - 1)) == 0 && (n & 0x55555555) != 0;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(1)$ thực hiện bằng một vài phép toán bit.
- **Space Complexity:** $O(1)$.

---

## 18. Reverse Bits

**Đề bài chi tiết:**
Cho số nguyên 32 bit không dấu (unsigned integer), hãy đảo ngược toàn bộ các bit của nó. (Ghi chú: Java không có kiểu int không dấu, do đó giá trị truyền vào được giả định là số có 32 bit đầy đủ).

**Phân tích thuật toán:**
Lặp qua 32 vị trí bit, mỗi lần đẩy kết quả hiện tại dịch trái 1 bit để nhường chỗ, sau đó chèn bit cuối cùng của `n` (bằng `n & 1`) vào. Tiếp theo, số nguyên `n` cần được dịch logic phải 1 bit (`>>> 1` trong Java, thay vì `>>` để tránh bị kéo theo bit dấu âm).

**Mã nguồn Java:**
```java
public class ReverseBitsClass {
    // Truyền vào kiểu int (bị xem là số có dấu) nhưng xử lý như 32 bit không dấu
    public int reverseBits(int n) {
        int result = 0;
        
        for (int i = 0; i < 32; i++) {
            result <<= 1;          // Tạo chỗ trống
            result |= (n & 1);     // Đẩy bit cuối của n vào result
            n >>>= 1;              // Dịch n phải 1 bit theo cách không giữ dấu
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(1)$ luôn lặp đúng 32 vòng.
- **Space Complexity:** $O(1)$.

---

## 19. Number of 1 Bits (Weight / Popcount)

**Đề bài chi tiết:**
Viết hàm nhận vào số nguyên không dấu 32-bit và trả về số lượng bit '1' có trong biểu diễn nhị phân của số đó (còn gọi là Hamming weight).

**Phân tích thuật toán:**
Một cách ngây thơ là vòng lặp 32 lần rồi kiểm tra từng bit. Tuy nhiên, thuật toán Brian Kernighan cho cách làm tối ưu hơn: phép tính `n = n & (n - 1)` luôn làm mất đi bit '1' có vị trí thấp nhất (phải nhất) của `n`. Số vòng lặp chỉ chạy phụ thuộc vào lượng bit 1 có trong `n` chứ không phải luôn lặp 32 vòng.

**Mã nguồn Java:**
```java
public class NumberOf1Bits {
    public int hammingWeight(int n) {
        int count = 0;
        while (n != 0) {
            // Xóa đi bit 1 phải nhất của n
            n &= (n - 1);
            count++;
        }
        return count;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(K)$ với $K$ là số lượng bit '1'. Trong trường hợp xấu nhất $O(32)$, trung bình sẽ chạy rất ít vòng.
- **Space Complexity:** $O(1)$.

---

## 20. Bitwise AND of Numbers Range

**Đề bài chi tiết:**
Cho hai số nguyên dương `left` và `right` đại diện cho khoảng đóng `[left, right]`. Trả về kết quả phép toán AND bitwise của toàn bộ các số nằm trong khoảng đó.

**Phân tích thuật toán:**
Phép AND của một dãy số liên tục sẽ thay đổi các bit dần về 0. Đặc tính quan trọng: kết quả sẽ là Tiền tố chung dài nhất (Longest Common Prefix - LCP) ở dạng nhị phân của số `left` và `right`, và đệm vào đó bằng toàn số 0 ở các bit phía sau.
Ta liên tục dịch phải `left` và `right` cho tới khi hai số này bằng nhau (để tìm tiền tố chung), dùng biến đếm theo dõi số lần đã dịch. Sau đó, dịch trái phần tiền tố trở lại để trả về kết quả đúng.

**Mã nguồn Java:**
```java
public class BitwiseAndNumbersRange {
    public int rangeBitwiseAnd(int left, int right) {
        int shift = 0;
        
        // Tìm Longest Common Prefix (LCP)
        while (left < right) {
            left >>= 1;
            right >>= 1;
            shift++;
        }
        
        // Dịch phần tiền tố LCP sang trái lại bấy nhiêu lần
        return left << shift;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(1)$ lặp lại tối đa 31 lần nếu khác biệt đến từ các bit cuối.
- **Space Complexity:** $O(1)$.

---

## 21. Majority Element

**Đề bài chi tiết:**
Cho một mảng kích thước $N$, tìm phần tử đa số (majority element). Phần tử đa số là phần tử xuất hiện nhiều hơn $\lfloor N / 2 \rfloor$ lần. Bạn có thể giả định mảng không rỗng và phần tử đa số luôn tồn tại.

**Phân tích thuật toán:**
Ngoài thuật toán Boyer-Moore Voting nổi tiếng tốn $O(N)$ thời gian, bài toán này có thể giải bằng Kỹ thuật Bitmask. Ta đếm số lượng bit 1 tại mỗi vị trí trong 32 bit của tất cả các phần tử. Nếu tổng số bit 1 tại một vị trí $i$ lớn hơn $N/2$, chứng tỏ phần tử đa số có bit 1 tại vị trí đó. Ta thiết lập bit 1 tại vị trí $i$ cho kết quả trả về.

**Mã nguồn Java:**
```java
public class MajorityElement {
    public int majorityElement(int[] nums) {
        int majority = 0;
        int n = nums.length;
        
        for (int i = 0; i < 32; i++) {
            int count = 0;
            for (int num : nums) {
                // Đếm số bit 1 tại cột i
                if (((num >> i) & 1) == 1) {
                    count++;
                }
            }
            // Nếu số lượng bit 1 > n / 2, bit đó thuộc về majority element
            if (count > n / 2) {
                majority |= (1 << i);
            }
        }
        
        return majority;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ (chính xác là $32 \times N$), quét mảng 32 lần.
- **Space Complexity:** $O(1)$.

---

## 22. UTF-8 Validation

**Đề bài chi tiết:**
Cho một mảng số nguyên đại diện cho luồng dữ liệu, hãy kiểm tra xem nó có phải là một dãy mã hóa UTF-8 hợp lệ hay không. Một ký tự UTF-8 có thể dài từ 1 đến 4 byte, với các bit đầu tiên của mỗi ký tự tuân theo quy tắc:
- 1-byte: `0xxxxxxx`
- 2-byte: `110xxxxx 10xxxxxx`
- 3-byte: `1110xxxx 10xxxxxx 10xxxxxx`
- 4-byte: `11110xxx 10xxxxxx 10xxxxxx 10xxxxxx`

**Phân tích thuật toán:**
Đọc từng số nguyên trong mảng. Nếu biến đếm số byte còn lại `remainingBytes` bằng 0, ta kiểm tra số byte của ký tự hiện tại bằng cách đếm số lượng bit 1 liên tiếp ở đầu bằng thao tác dịch bit (`>> 7`, `>> 5`, `>> 4`, `>> 3`).
Nếu `remainingBytes > 0`, ta kiểm tra xem byte hiện tại (phần thân ký tự) có bắt đầu bằng `10` hay không bằng cách dùng dịch phải `>> 6` để kiểm tra có khớp với bit mẫu `0b10` hay không. 

**Mã nguồn Java:**
```java
public class UTF8Validation {
    public boolean validUtf8(int[] data) {
        int remainingBytes = 0;
        
        for (int num : data) {
            if (remainingBytes == 0) {
                // Kiểm tra bit header cho ký tự mới
                if ((num >> 7) == 0b0) {
                    remainingBytes = 0; // 1-byte
                } else if ((num >> 5) == 0b110) {
                    remainingBytes = 1; // 2-byte
                } else if ((num >> 4) == 0b1110) {
                    remainingBytes = 2; // 3-byte
                } else if ((num >> 3) == 0b11110) {
                    remainingBytes = 3; // 4-byte
                } else {
                    return false; // Header không hợp lệ
                }
            } else {
                // Kiểm tra phần thân (body byte) luôn bắt đầu bằng 10xxxxxx
                if ((num >> 6) != 0b10) {
                    return false;
                }
                remainingBytes--;
            }
        }
        
        return remainingBytes == 0;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ quét mảng đúng 1 lần.
- **Space Complexity:** $O(1)$.

---

## 23. Decode XORed Array

**Đề bài chi tiết:**
Có một mảng số nguyên ẩn `arr` bao gồm $n$ phần tử. Mảng này được mã hóa thành mảng `encoded` có độ dài $n - 1$ theo quy tắc `encoded[i] = arr[i] XOR arr[i + 1]`. Cho mảng `encoded` và giá trị của phần tử đầu tiên của mảng ẩn là `first`, hãy giải mã để tìm lại mảng `arr`.

**Phân tích thuật toán:**
Dựa trên tính chất của XOR, nếu ta có `a ^ b = c` thì suy ra được `a = b ^ c` và `b = a ^ c`.
Ta có phương trình tạo mảng: `encoded[i] = arr[i] ^ arr[i+1]`.
Thay đổi vế sẽ được: `arr[i+1] = arr[i] ^ encoded[i]`.
Vì đã được cung cấp `arr[0] = first`, ta có thể tính tuần tự các phần tử tiếp theo của mảng ẩn thông qua phép toán này.

**Mã nguồn Java:**
```java
public class DecodeXORedArray {
    public int[] decode(int[] encoded, int first) {
        int n = encoded.length;
        int[] arr = new int[n + 1];
        arr[0] = first;
        
        for (int i = 0; i < n; i++) {
            // Phần tử sau bằng phần tử trước XOR với giá trị mã hóa
            arr[i + 1] = arr[i] ^ encoded[i];
        }
        
        return arr;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ với $N$ là độ dài mảng `encoded`.
- **Space Complexity:** $O(N)$ để tạo mảng kết quả.

---

## 24. XOR Queries of a Subarray

**Đề bài chi tiết:**
Cho một mảng số nguyên dương `arr` và mảng các truy vấn `queries`, trong đó `queries[i] = [L_i, R_i]`. Đối với mỗi truy vấn, yêu cầu tính toán giá trị XOR của đoạn phần tử con từ chỉ số $L_i$ đến $R_i$ (tức là `arr[L_i] ^ arr[L_i+1] ^ ... ^ arr[R_i]`). Trả về kết quả là một mảng tổng hợp các lời giải cho toàn bộ truy vấn.

**Phân tích thuật toán:**
Nếu tính toán lại từ đầu cho mỗi truy vấn thì độ phức tạp sẽ là $O(Q \times N)$ gây ra Timeout. Ta có thể dùng mảng Prefix XOR (Cộng dồn XOR) để trả lời truy vấn trong $O(1)$.
Gọi `prefix[i]` là XOR của đoạn tử từ `arr[0]` đến `arr[i-1]`.
Khi đó XOR đoạn từ $L$ đến $R$ sẽ là: `prefix[R + 1] ^ prefix[L]`. Tính chất này là do phần tử nằm trước $L$ đã bị triệt tiêu khi tự XOR với chính nó qua mảng prefix.

**Mã nguồn Java:**
```java
public class XORQueriesSubarray {
    public int[] xorQueries(int[] arr, int[][] queries) {
        int n = arr.length;
        int[] prefix = new int[n + 1];
        
        // Tính mảng Prefix XOR
        for (int i = 0; i < n; i++) {
            prefix[i + 1] = prefix[i] ^ arr[i];
        }
        
        int[] result = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            int L = queries[i][0];
            int R = queries[i][1];
            // Lấy đoạn XOR bằng phép triệt tiêu
            result[i] = prefix[R + 1] ^ prefix[L];
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N + Q)$ với $N$ là kích thước `arr` và $Q$ là lượng câu hỏi `queries`.
- **Space Complexity:** $O(N)$ lưu bộ nhớ Prefix.

---

## 25. Minimum Number of Flips to Convert a Number

**Đề bài chi tiết:**
Cho 3 số nguyên dương `a`, `b` và `c`. Trả về số lượng thao tác lật bit (chuyển bit từ 0 sang 1 hoặc ngược lại) tối thiểu áp dụng trên `a` và `b` để thỏa mãn biểu thức `a OR b == c`.

**Phân tích thuật toán:**
Duyệt lần lượt từng vị trí bit của `a`, `b` và `c`. Tại một cột bit bất kỳ:
- Nếu bit tương ứng của `c` là 1: ta cần ít nhất một trong hai bit của `a` hoặc `b` là 1. Nếu cả hai đều bằng 0, ta bắt buộc phải tốn 1 lần lật bit.
- Nếu bit tương ứng của `c` là 0: cả bit của `a` và `b` đều bắt buộc phải mang giá trị 0. Do đó, số lần cần lật chính là tổng giá trị bit hiện tại của `a` và `b` cộng lại (ví dụ nếu cả hai là 1, thì phải lật 2 bit).

**Mã nguồn Java:**
```java
public class MinFlipsToConvert {
    public int minFlips(int a, int b, int c) {
        int flips = 0;
        
        for (int i = 0; i < 32; i++) {
            int bitA = (a >> i) & 1;
            int bitB = (b >> i) & 1;
            int bitC = (c >> i) & 1;
            
            if (bitC == 1) {
                if (bitA == 0 && bitB == 0) {
                    flips++;
                }
            } else {
                // Nếu bitC == 0 thì cần lật bất kì bit nào bằng 1 thành 0
                flips += bitA + bitB;
            }
        }
        
        return flips;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(1)$ lặp lại đúng 32 lần.
- **Space Complexity:** $O(1)$.

---

## 26. Count Triplets That Can Form Two Arrays of Equal XOR

**Đề bài chi tiết:**
Cho mảng số nguyên `arr`. Cần chọn ra ba chỉ số `i`, `j` và `k` sao cho `0 <= i < j <= k < arr.length`.
Ta định nghĩa 2 mảng con có giá trị XOR lần lượt là `a = arr[i] ^ ... ^ arr[j - 1]` và `b = arr[j] ^ ... ^ arr[k]`.
Trả về số lượng bộ ba `(i, j, k)` sao cho `a == b`.

**Phân tích thuật toán:**
Điều kiện `a == b` trong thế giới bit đồng nghĩa với `a ^ b == 0`.
Thế vào biểu thức ta có `arr[i] ^ arr[i+1] ^ ... ^ arr[k] == 0`.
Như vậy, nếu ta phát hiện một đoạn từ `i` đến `k` có tổng XOR bằng 0, thì bất kỳ vị trí `j` nào (chọn điểm chẻ) cắt đoạn ra làm 2 thỏa mãn `i < j <= k` đều tạo ra một bộ ba thỏa điều kiện `a == b`. Số lượng vị trí `j` có thể đặt là `k - i`. Ta sẽ duyệt qua mọi điểm đầu và điểm cuối để dò đoạn có XOR bằng 0.

**Mã nguồn Java:**
```java
public class CountTripletsEqualXOR {
    public int countTriplets(int[] arr) {
        int n = arr.length;
        int count = 0;
        
        for (int i = 0; i < n - 1; i++) {
            int xorSum = arr[i];
            for (int k = i + 1; k < n; k++) {
                xorSum ^= arr[k];
                if (xorSum == 0) {
                    // Mọi vị trí j nằm giữa (i, k] đều thỏa
                    count += (k - i);
                }
            }
        }
        
        return count;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N^2)$ duyệt qua các cặp chỉ số `(i, k)`. (Có thể tối ưu xuống $O(N)$ bằng HashTable chứa Prefix).
- **Space Complexity:** $O(1)$.

---

## 27. Sort Integers by The Number of 1 Bits

**Đề bài chi tiết:**
Cho mảng số nguyên `arr`. Hãy sắp xếp mảng tăng dần theo số lượng bit 1 tồn tại trong biểu diễn nhị phân của phần tử. Trong trường hợp có các phần tử có chung số lượng bit 1, hãy sắp xếp chúng theo giá trị nguyên thủy tăng dần.

**Phân tích thuật toán:**
Bài toán yêu cầu tùy biến bộ so sánh (Custom Comparator). Việc đếm số bit 1 dễ dàng làm được bằng hàm `Integer.bitCount(num)`. Để cải thiện tốc độ, ta bọc lại mảng thành kiểu tham chiếu `Integer` để gọi hàm thư viện `Arrays.sort()` truyền theo bộ quy định Custom Comparator so sánh bit trước, sau đó fallback sang so sánh số.

**Mã nguồn Java:**
```java
import java.util.Arrays;

public class SortByBits {
    public int[] sortByBits(int[] arr) {
        Integer[] boxedArr = new Integer[arr.length];
        for (int i = 0; i < arr.length; i++) {
            boxedArr[i] = arr[i];
        }
        
        Arrays.sort(boxedArr, (a, b) -> {
            int countA = Integer.bitCount(a);
            int countB = Integer.bitCount(b);
            if (countA == countB) {
                return Integer.compare(a, b);
            }
            return Integer.compare(countA, countB);
        });
        
        for (int i = 0; i < arr.length; i++) {
            arr[i] = boxedArr[i];
        }
        return arr;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N \log N)$ theo hệ thuật toán QuickSort.
- **Space Complexity:** $O(N)$ bộ nhớ bọc đối tượng (Boxing).

---

## 28. Maximum XOR of Two Numbers in an Array

**Đề bài chi tiết:**
Cho mảng số nguyên `nums`, trả về giá trị XOR lớn nhất giữa một cặp số bất kỳ trong mảng (tức là max của `nums[i] ^ nums[j]`). Giải thuật yêu cầu xử lý xong trong thời gian $O(N)$.

**Phân tích thuật toán:**
Bài toán này được giải tối ưu thông qua việc biến đổi số nguyên thành chuỗi 32 bit, sau đó đẩy lên cấu trúc cây Trie.
- Quét qua mảng, đẩy mọi bit từ trái qua phải (MSB sang LSB) vào Trie.
- Quét lại lần 2, với mỗi số nguyên ta lấy bit cao nhất, sau đó đi xuống Trie theo nhánh ưu tiên các bit "đảo ngược" với bit hiện tại (Bit 1 tìm nhánh 0, bit 0 tìm nhánh 1) bằng chiến thuật tham lam (Greedy). Điều này đảm bảo kết quả sinh ra bit 1 ở hàng bit cao nhất tạo ra giá trị lớn nhất.

**Mã nguồn Java:**
```java
public class MaximumXORTwoNumbers {
    class TrieNode {
        TrieNode[] children = new TrieNode[2];
    }
    
    public int findMaximumXOR(int[] nums) {
        TrieNode root = new TrieNode();
        
        // Tạo Trie 32 cấp
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
        
        int maxTotalXOR = 0;
        // Đi ngược Trie để tìm max XOR
        for (int num : nums) {
            TrieNode curr = root;
            int currentMaxXOR = 0;
            for (int i = 31; i >= 0; i--) {
                int bit = (num >> i) & 1;
                int toggledBit = 1 - bit; // Nhánh ngược bit mong đợi
                
                if (curr.children[toggledBit] != null) {
                    currentMaxXOR |= (1 << i);
                    curr = curr.children[toggledBit];
                } else {
                    curr = curr.children[bit];
                }
            }
            maxTotalXOR = Math.max(maxTotalXOR, currentMaxXOR);
        }
        
        return maxTotalXOR;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(32 \cdot N) = O(N)$. Duyệt n số lặp 32 lần cho mỗi số.
- **Space Complexity:** $O(32 \cdot N)$ số Node trên Trie.

---

## 29. Divide Two Integers

**Đề bài chi tiết:**
Chia hai số nguyên `dividend` (số bị chia) và `divisor` (số chia) mà không sử dụng các toán tử nguyên gốc: nhân, chia và chia lấy dư.
Trả về thương số nguyên. Giả sử hệ thống xử lý số nguyên 32-bit có dấu. Nếu thương số vượt qua giới hạn của hệ thống là `[-2^31, 2^31 - 1]`, hãy ghim lại bằng `2^31 - 1`.

**Phân tích thuật toán:**
Để làm phép chia mà không chia, ta dùng cách cộng/trừ luỹ tiến thông minh hơn. Không trừ dần một cục `divisor` (sẽ sinh $O(N)$), ta dùng kỹ thuật nhân đôi luỹ thừa (Exponential Search): dịch trái số `divisor` ($a \times 2, a \times 4, \dots, a \times 2^k$) sao cho kết quả nhân vẫn $<=$ `dividend`. Trừ lượng khổng lồ đó đi, cộng số $2^k$ vào thương, sau đó thực hiện lại đến khi nhỏ hơn phần dư. Cẩn thận dùng Long để tránh Overflow trong vòng lặp luỹ tiến.

**Mã nguồn Java:**
```java
public class DivideTwoIntegers {
    public int divide(int dividend, int divisor) {
        // Xử lý góc edge-case tràn số nguyên
        if (dividend == Integer.MIN_VALUE && divisor == -1) {
            return Integer.MAX_VALUE;
        }
        
        boolean isNegative = (dividend < 0) ^ (divisor < 0);
        long absDividend = Math.abs((long) dividend);
        long absDivisor = Math.abs((long) divisor);
        
        int quotient = 0;
        // Trừ dần các khối lũy thừa 2 lớn nhất
        while (absDividend >= absDivisor) {
            long temp = absDivisor, multiple = 1;
            while (absDividend >= (temp << 1)) {
                temp <<= 1;
                multiple <<= 1;
            }
            absDividend -= temp;
            quotient += multiple;
        }
        
        return isNegative ? -quotient : quotient;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(\log^2 N)$ với $N$ là kết quả thương. Do quá trình dò lớn nhất cũng chỉ lặp nhiều nhất $\approx 31$ bước.
- **Space Complexity:** $O(1)$.

---

## 30. Traveling Salesperson Problem (TSP)

**Đề bài chi tiết:**
Một người giao hàng xuất phát từ thành phố số 0, cần đi qua tất cả $N - 1$ thành phố còn lại (mỗi nơi đi đúng một lần) và quay trở về lại 0. Có một ma trận chi phí biểu diễn khoảng cách giữa các thành phố. Tìm chi phí đi lại có tổng nhỏ nhất.

**Phân tích thuật toán:**
Bài toán NP-Hard này được giải thông qua Quy hoạch động (Bitmask DP) nhằm cải thiện $O(N!)$ xuống cấp tiến.
Sử dụng một con số bitmask $N$ bit làm trạng thái, bit bằng 1 chỉ ra đã đi qua thành phố tương ứng.
Trạng thái quy hoạch động `dp[mask][i]` tượng trưng cho: chi phí tối thiểu để khám phá hết tập hợp các thành phố được bật trong `mask`, và dừng chân hiện tại ở thành phố `i`. Công thức là truy ngược từ `i` để đi tới các thành phố rỗng, và lưu kết quả nhớ.

**Mã nguồn Java:**
```java
import java.util.Arrays;

public class TSP {
    public int tsp(int[][] dist) {
        int n = dist.length;
        int VISITED_ALL = (1 << n) - 1;
        // Bảng dp kích thước [2^n][n]
        int[][] dp = new int[1 << n][n];
        
        for (int[] row : dp) {
            Arrays.fill(row, -1); // Uncomputed mark
        }
        
        // Khởi hành với chỉ bit số 0 bật (thăm 0)
        return solve(1, 0, dist, dp, VISITED_ALL, n);
    }
    
    private int solve(int mask, int pos, int[][] dist, int[][] dp, int visitedAll, int n) {
        if (mask == visitedAll) {
            return dist[pos][0]; // Trở về gốc
        }
        
        if (dp[mask][pos] != -1) {
            return dp[mask][pos];
        }
        
        int ans = Integer.MAX_VALUE;
        // Đi thử thành phố khác
        for (int city = 0; city < n; city++) {
            if ((mask & (1 << city)) == 0) { // Nếu city chưa thăm
                int newAns = dist[pos][city] + solve(mask | (1 << city), city, dist, dp, visitedAll, n);
                ans = Math.min(ans, newAns);
            }
        }
        
        return dp[mask][pos] = ans;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N^2 \cdot 2^N)$. Tổng cộng $N \times 2^N$ trạng thái, mỗi lần mất tối đa $O(N)$ vòng lặp.
- **Space Complexity:** $O(N \cdot 2^N)$ phân bổ bảng nhớ.
