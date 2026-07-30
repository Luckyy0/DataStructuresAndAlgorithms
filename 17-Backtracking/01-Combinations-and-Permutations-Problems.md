# 01 - Bài tập: Combinations and Permutations (Tổ hợp và Hoán vị)

Tài liệu này bao gồm 30 bài tập về Backtracking, tập trung vào Subsets, Combinations, Permutations và các biến thể. 10 bài đầu tiên sẽ có đầy đủ đề bài, phân tích, code Java và độ phức tạp. 20 bài sau được tóm tắt.

## 1. Subsets (LeetCode 78)
**Đề bài chi tiết:** Cho một mảng số nguyên `nums` chứa các phần tử duy nhất, trả về tất cả các tập con có thể có (power set). Giải pháp không được chứa các tập con trùng lặp. Trả về kết quả dưới dạng mảng của các mảng theo bất kỳ thứ tự nào.

**Phân tích thuật toán:**
Sử dụng Backtracking. Bắt đầu với một mảng rỗng. Tại mỗi bước `i` (từ `start` đến hết mảng), ta chọn `nums[i]` thêm vào danh sách tạm, đệ quy tiếp với `start = i + 1`, sau đó loại bỏ `nums[i]` (un-choose) để thử phần tử khác. Mọi trạng thái của danh sách tạm đều là một tập con hợp lệ.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(), nums, 0);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> temp, int[] nums, int start) {
        result.add(new ArrayList<>(temp)); // Mọi bước đều sinh ra một subset
        for (int i = start; i < nums.length; i++) {
            temp.add(nums[i]);
            backtrack(result, temp, nums, i + 1);
            temp.remove(temp.size() - 1);
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \times 2^N)$, vì có $2^N$ tập con và mỗi tập con mất $O(N)$ để copy vào `result`.
- Space Complexity: $O(N)$ cho call stack và mảng `temp`.

---

## 2. Subsets II (LeetCode 90)
**Đề bài chi tiết:** Cho một mảng số nguyên `nums` có thể chứa các phần tử trùng lặp, trả về tất cả các tập con có thể có. Giải pháp không được chứa các tập con trùng lặp.

**Phân tích thuật toán:**
Để xử lý trùng lặp, ta bắt buộc phải sắp xếp mảng đầu vào. Trong vòng lặp `for`, nếu `i > start` và `nums[i] == nums[i-1]`, ta bỏ qua phần tử này vì nó sẽ sinh ra nhánh giống hệt nhánh trước đó tại cùng một độ sâu đệ quy.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(nums); // Sort để gom duplicates
        backtrack(result, new ArrayList<>(), nums, 0);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> temp, int[] nums, int start) {
        result.add(new ArrayList<>(temp));
        for (int i = start; i < nums.length; i++) {
            if (i > start && nums[i] == nums[i - 1]) continue; // Bỏ qua trùng lặp
            temp.add(nums[i]);
            backtrack(result, temp, nums, i + 1);
            temp.remove(temp.size() - 1);
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \times 2^N)$.
- Space Complexity: $O(N)$ đệ quy call stack.

---

## 3. Combinations (LeetCode 77)
**Đề bài chi tiết:** Cho hai số nguyên `n` và `k`, trả về tất cả các tổ hợp (combinations) có thể có chứa `k` số được chọn từ khoảng từ `1` đến `n`.

**Phân tích thuật toán:**
Tương tự như Subsets, nhưng ta chỉ lưu vào `result` khi độ dài của `temp` bằng `k`. Vòng lặp `for` chạy từ `start` (bắt đầu bằng 1) đến `n`. Ta cũng có thể thêm điều kiện pruning (tối ưu cắt tỉa) để dừng sớm nếu số lượng phần tử còn lại không đủ `k`.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> combine(int n, int k) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(), n, k, 1);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> temp, int n, int k, int start) {
        if (temp.size() == k) {
            result.add(new ArrayList<>(temp));
            return;
        }
        
        // Pruning: Không đủ phần tử để tạo mảng độ dài k
        for (int i = start; i <= n - (k - temp.size()) + 1; i++) {
            temp.add(i);
            backtrack(result, temp, n, k, i + 1);
            temp.remove(temp.size() - 1);
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(k \times C(n, k))$.
- Space Complexity: $O(k)$ (Call Stack).

---

## 4. Permutations (LeetCode 46)
**Đề bài chi tiết:** Cho một mảng `nums` gồm các số nguyên riêng biệt, trả về tất cả các hoán vị có thể có. Có thể trả kết quả theo bất kỳ thứ tự nào.

**Phân tích thuật toán:**
Với hoán vị, ở mỗi bước ta có thể chọn bất kỳ phần tử nào chưa được chọn trong mảng. Do đó, vòng lặp `for` luôn chạy từ `0` thay vì `start`. Ta cần một mảng boolean `used` để đánh dấu phần tử nào đã được thêm vào `temp`.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(), nums, new boolean[nums.length]);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> temp, int[] nums, boolean[] used) {
        if (temp.size() == nums.length) {
            result.add(new ArrayList<>(temp));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            if (used[i]) continue; // Đã dùng phần tử này
            
            used[i] = true;
            temp.add(nums[i]);
            backtrack(result, temp, nums, used);
            used[i] = false;
            temp.remove(temp.size() - 1);
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \times N!)$.
- Space Complexity: $O(N)$ (Stack và `used` array).

---

## 5. Permutations II (LeetCode 47)
**Đề bài chi tiết:** Cho một bộ sưu tập các số nguyên `nums` có thể chứa các bản sao trùng lặp, trả về tất cả các hoán vị duy nhất có thể.

**Phân tích thuật toán:**
Ta cần sắp xếp mảng đầu vào `nums` để gom các số trùng lặp lại với nhau. Cần dùng mảng `used`. Khác biệt nằm ở chỗ ta phải bỏ qua (skip) việc xử lý nếu `i > 0 && nums[i] == nums[i-1] && !used[i-1]`. Điều này đảm bảo các phần tử trùng lặp được xử lý theo cùng một thứ tự tương đối duy nhất.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(nums);
        backtrack(result, new ArrayList<>(), nums, new boolean[nums.length]);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> temp, int[] nums, boolean[] used) {
        if (temp.size() == nums.length) {
            result.add(new ArrayList<>(temp));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            if (used[i]) continue;
            // Bỏ qua nếu là phần tử trùng lặp và phần tử trước đó chưa được dùng trong branch hiện tại
            if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) continue;
            
            used[i] = true;
            temp.add(nums[i]);
            backtrack(result, temp, nums, used);
            used[i] = false;
            temp.remove(temp.size() - 1);
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \times N!)$ trong trường hợp tồi tệ nhất (các phần tử đều khác nhau).
- Space Complexity: $O(N)$ (Stack và mảng boolean).

---

## 6. Letter Case Permutation (LeetCode 784)
**Đề bài chi tiết:** Cho một chuỗi `s`, ta có thể chuyển từng chữ cái riêng lẻ thành chữ thường hoặc chữ hoa để tạo chuỗi khác. Trả về danh sách tất cả các chuỗi có thể có.

**Phân tích thuật toán:**
Duyệt qua từng ký tự của `s` từ trái sang phải bằng Backtracking. Nếu ký tự là chữ số, giữ nguyên và tiếp tục với ký tự tiếp theo. Nếu là chữ cái, phân nhánh làm 2 con đường: một nhánh chuyển thành chữ hoa, một nhánh chuyển thành chữ thường.

**Mã nguồn Java:**
```java
class Solution {
    public List<String> letterCasePermutation(String s) {
        List<String> result = new ArrayList<>();
        backtrack(result, s.toCharArray(), 0);
        return result;
    }

    private void backtrack(List<String> result, char[] chars, int index) {
        if (index == chars.length) {
            result.add(new String(chars));
            return;
        }

        if (Character.isLetter(chars[index])) {
            // Nhánh 1: Chữ hoa
            chars[index] = Character.toUpperCase(chars[index]);
            backtrack(result, chars, index + 1);
            
            // Nhánh 2: Chữ thường
            chars[index] = Character.toLowerCase(chars[index]);
            backtrack(result, chars, index + 1);
        } else {
            // Ký tự là số, đi tiếp
            backtrack(result, chars, index + 1);
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \times 2^L)$, với $N$ là độ dài của $S$ và $L$ là số lượng chữ cái trong $S$.
- Space Complexity: $O(N)$ (recursion stack, char array depth).

---

## 7. Iterator for Combination (LeetCode 1286)
**Đề bài chi tiết:** Thiết kế lớp `CombinationIterator` nhận đầu vào là chuỗi các chữ cái nhỏ phân biệt đã được sắp xếp `characters` và độ dài tổ hợp `combinationLength`. Hàm `next()` trả về combination kế tiếp theo thứ tự từ điển, và hàm `hasNext()` trả về `true` nếu còn combination tiếp theo.

**Phân tích thuật toán:**
Sử dụng backtracking tạo trước tất cả các combinations và lưu vào một `Queue` hoặc `List`. Do chuỗi đầu vào đã sắp xếp và thuật toán duyệt theo chiều sâu tạo mảng nên kết quả có được tự động tuân theo thứ tự từ điển.

**Mã nguồn Java:**
```java
class CombinationIterator {
    private Queue<String> queue = new LinkedList<>();

    public CombinationIterator(String characters, int combinationLength) {
        backtrack(characters, combinationLength, 0, new StringBuilder());
    }
    
    private void backtrack(String chars, int k, int start, StringBuilder temp) {
        if (temp.length() == k) {
            queue.offer(temp.toString());
            return;
        }
        for (int i = start; i < chars.length(); i++) {
            temp.append(chars.charAt(i));
            backtrack(chars, k, i + 1, temp);
            temp.deleteCharAt(temp.length() - 1);
        }
    }

    public String next() {
        return queue.poll();
    }

    public boolean hasNext() {
        return !queue.isEmpty();
    }
}
```

**Độ phức tạp:**
- Time Complexity: Khởi tạo tốn $O(k \times C(N, k))$ thời gian tính toán. `next()` và `hasNext()` tốn $O(1)$.
- Space Complexity: $O(k \times C(N, k))$ để lưu vào bộ nhớ trong cấu trúc dữ liệu Queue.

---

## 8. Beautiful Arrangement (LeetCode 526)
**Đề bài chi tiết:** Giả sử bạn có `n` số nguyên từ `1` đến `n`. Chúng ta định nghĩa một mảng đẹp (beautiful arrangement) nếu với mỗi `i` (từ $1$ đến $n$), thì hoặc $nums[i]$ chia hết cho $i$, hoặc $i$ chia hết cho $nums[i]$. Hãy đếm số mảng đẹp có thể được tạo ra.

**Phân tích thuật toán:**
Đây là bài toán tương tự như Permutation có điều kiện Pruning (cắt tỉa). Chúng ta sử dụng một mảng `used` để đánh dấu số đã chọn. Biến đếm `pos` sẽ theo dõi vị trí $i$ hiện tại ($pos$ từ $1$ đến $n$). Bất kỳ lúc nào ràng buộc trên bị vi phạm, nhánh đệ quy hiện tại sẽ dừng ngay lập tức.

**Mã nguồn Java:**
```java
class Solution {
    int count = 0;

    public int countArrangement(int n) {
        boolean[] used = new boolean[n + 1];
        backtrack(n, 1, used);
        return count;
    }

    private void backtrack(int n, int pos, boolean[] used) {
        if (pos > n) {
            count++;
            return;
        }

        for (int i = 1; i <= n; i++) {
            if (!used[i] && (i % pos == 0 || pos % i == 0)) {
                used[i] = true;
                backtrack(n, pos + 1, used);
                used[i] = false;
            }
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(K)$, với $K$ là số trạng thái hợp lệ. (Nhỏ hơn rất nhiều so với $O(N!)$ do pruning tốt).
- Space Complexity: $O(N)$ (recursion stack và mảng boolean used).

---

## 9. Count Sorted Vowel Strings (LeetCode 1641)
**Đề bài chi tiết:** Cho số nguyên `n`, đếm số lượng chuỗi có độ dài `n` chỉ bao gồm nguyên âm ('a', 'e', 'i', 'o', 'u') và có các ký tự được sắp xếp theo thứ tự từ điển tăng dần.

**Phân tích thuật toán:**
Có thể giải bằng Backtracking hoặc Quy hoạch động (DP). Để biểu diễn thuật toán, ta dùng `backtrack`. Vì chuỗi cần sắp xếp từ điển, khi chọn một ký tự ở chỉ số `i`, ở ký tự tiếp theo ta chỉ có thể chọn từ `i` trở đi.
Danh sách các nguyên âm: `a, e, i, o, u` tương đương với chỉ số từ `0` đến `4`.

**Mã nguồn Java:**
```java
class Solution {
    int count = 0;

    public int countVowelStrings(int n) {
        backtrack(n, 0, 0);
        return count;
    }

    private void backtrack(int n, int start, int length) {
        if (length == n) {
            count++;
            return;
        }
        for (int i = start; i < 5; i++) {
            // Cho phép chọn lại i (i thay vì i + 1)
            backtrack(n, i, length + 1);
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(C(n+4, 4))$. (Thuật toán có thể tối ưu thành $O(N)$ bằng DP).
- Space Complexity: $O(N)$ (recursion stack).

---

## 10. Sequential Digits (LeetCode 1291)
**Đề bài chi tiết:** Một số nguyên có các chữ số liên tiếp (sequential digits) nếu và chỉ nếu mỗi chữ số sau trong số đó lớn hơn chữ số trước đúng một đơn vị. Trả về danh sách chứa các số nguyên như vậy thuộc phạm vi `[low, high]` và đã được sắp xếp.

**Phân tích thuật toán:**
Dùng Backtracking tạo các số có chữ số liên tiếp bắt đầu từ $1$ đến $9$. Tại mỗi đệ quy, lấy số trước nhân với 10 rồi cộng thêm chữ số trước `+ 1`. Nếu số sinh ra lọt vào khoảng `[low, high]` thì thêm vào danh sách kết quả. Nếu lớn hơn `high` thì dừng nhánh đó.

**Mã nguồn Java:**
```java
class Solution {
    public List<Integer> sequentialDigits(int low, int high) {
        List<Integer> result = new ArrayList<>();
        
        for (int startDigit = 1; startDigit <= 9; startDigit++) {
            backtrack(result, low, high, startDigit, startDigit);
        }
        
        Collections.sort(result); // Kết quả trả về cần được sắp xếp
        return result;
    }

    private void backtrack(List<Integer> result, int low, int high, int num, int lastDigit) {
        if (num >= low && num <= high) {
            result.add(num);
        }
        
        if (num > high || lastDigit == 9) {
            return;
        }
        
        int nextDigit = lastDigit + 1;
        int nextNum = num * 10 + nextDigit;
        backtrack(result, low, high, nextNum, nextDigit);
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(1)$. Tổng số sequential digits sinh ra rất bé (tối đa 36 số). Sắp xếp mất $O(1)$.
- Space Complexity: $O(1)$ để lưu call stack (sâu nhất là 9 mức).

---

## 11. Combination Sum (LeetCode 39)
**Đề bài chi tiết:** Cho một mảng `candidates` gồm các số nguyên phân biệt và một số nguyên `target`, trả về danh sách tất cả các tổ hợp duy nhất của `candidates` mà tổng các số được chọn bằng `target`. Có thể chọn một số lượng không giới hạn các phần tử giống nhau từ `candidates`.

**Phân tích thuật toán:**
Tại mỗi bước đệ quy, ta có thể chọn phần tử hiện tại và tiếp tục giữ chỉ số hiện tại (cho phép lặp lại), hoặc không chọn phần tử hiện tại và chuyển sang phần tử tiếp theo. Nếu tổng vượt quá `target` thì dừng (pruning).

**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(), candidates, target, 0);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> temp, int[] candidates, int remain, int start) {
        if (remain < 0) return;
        if (remain == 0) {
            result.add(new ArrayList<>(temp));
            return;
        }
        for (int i = start; i < candidates.length; i++) {
            temp.add(candidates[i]);
            // Không tăng i để có thể tái sử dụng phần tử này
            backtrack(result, temp, candidates, remain - candidates[i], i);
            temp.remove(temp.size() - 1);
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N^{\frac{T}{M}})$ với $N$ là số lượng candidate, $T$ là target, $M$ là giá trị nhỏ nhất trong mảng.
- Space Complexity: $O(\frac{T}{M})$ (call stack).

---

## 12. Combination Sum II (LeetCode 40)
**Đề bài chi tiết:** Cho một tập hợp `candidates` (có thể chứa các phần tử trùng lặp) và một số nguyên `target`, tìm tất cả các tổ hợp duy nhất sao cho tổng các số bằng `target`. Mỗi phần tử chỉ được sử dụng một lần trong một tổ hợp.

**Phân tích thuật toán:**
Sắp xếp mảng trước để gom các phần tử trùng lặp. Khi duyệt, nếu phần tử hiện tại giống phần tử trước đó và cả hai nằm trong cùng vòng lặp (tức `i > start`), thì bỏ qua để tránh trùng lặp tổ hợp ở cùng một mức đệ quy.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(candidates);
        backtrack(result, new ArrayList<>(), candidates, target, 0);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> temp, int[] candidates, int remain, int start) {
        if (remain < 0) return;
        if (remain == 0) {
            result.add(new ArrayList<>(temp));
            return;
        }
        for (int i = start; i < candidates.length; i++) {
            // Bỏ qua phần tử trùng lặp ở cùng mức
            if (i > start && candidates[i] == candidates[i - 1]) continue;
            
            temp.add(candidates[i]);
            backtrack(result, temp, candidates, remain - candidates[i], i + 1);
            temp.remove(temp.size() - 1);
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(2^N)$ trong trường hợp tồi tệ nhất.
- Space Complexity: $O(N)$.

---

## 13. Combination Sum III (LeetCode 216)
**Đề bài chi tiết:** Tìm tất cả các tổ hợp hợp lệ của `k` số có tổng bằng `n` sao cho các điều kiện sau được thỏa mãn: Chỉ sử dụng các số từ `1` đến `9`. Mỗi số được sử dụng tối đa một lần.

**Phân tích thuật toán:**
Dùng Backtracking duyệt các số từ 1 đến 9, theo dõi số lượng phần tử hiện có và tổng còn lại. Nếu đủ `k` phần tử và tổng còn lại là 0 thì lưu vào kết quả. Điều kiện cắt tỉa: kích thước mảng vượt quá `k` hoặc tổng âm.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> combinationSum3(int k, int n) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(), k, n, 1);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> temp, int k, int remain, int start) {
        if (temp.size() > k || remain < 0) return;
        if (temp.size() == k && remain == 0) {
            result.add(new ArrayList<>(temp));
            return;
        }
        for (int i = start; i <= 9; i++) {
            temp.add(i);
            backtrack(result, temp, k, remain - i, i + 1);
            temp.remove(temp.size() - 1);
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(K \times C(9, K))$.
- Space Complexity: $O(K)$.

---

## 14. Palindrome Partitioning (LeetCode 131)
**Đề bài chi tiết:** Cho chuỗi `s`, phân chia `s` sao cho mọi chuỗi con trong phân vùng đều là chuỗi đối xứng (palindrome). Trả về tất cả các cách phân chia có thể.

**Phân tích thuật toán:**
Duyệt qua tất cả các vị trí cắt chuỗi (từ `start` đến cuối chuỗi). Nếu tiền tố từ `start` đến vị trí cắt `i` là palindrome, ta lưu tiền tố này vào mảng tạm và tiếp tục đệ quy để cắt phần còn lại của chuỗi (từ `i + 1`).

**Mã nguồn Java:**
```java
class Solution {
    public List<List<String>> partition(String s) {
        List<List<String>> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(), s, 0);
        return result;
    }

    private void backtrack(List<List<String>> result, List<String> temp, String s, int start) {
        if (start == s.length()) {
            result.add(new ArrayList<>(temp));
            return;
        }
        for (int i = start; i < s.length(); i++) {
            if (isPalindrome(s, start, i)) {
                temp.add(s.substring(start, i + 1));
                backtrack(result, temp, s, i + 1);
                temp.remove(temp.size() - 1);
            }
        }
    }

    private boolean isPalindrome(String s, int left, int right) {
        while (left < right) {
            if (s.charAt(left++) != s.charAt(right--)) return false;
        }
        return true;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \times 2^N)$. Có tối đa $2^{N-1}$ cách chia.
- Space Complexity: $O(N)$ độ sâu của ngăn xếp đệ quy.

---

## 15. Generate Parentheses (LeetCode 22)
**Đề bài chi tiết:** Cho số nguyên `n` đại diện cho `n` cặp dấu ngoặc đơn, hãy viết một hàm để tạo tất cả các tổ hợp dấu ngoặc đơn hợp lệ.

**Phân tích thuật toán:**
Duy trì 2 biến đếm `open` và `close`. Ta có thể thêm dấu `(` nếu `open < n`. Ta có thể thêm dấu `)` nếu `close < open`. Nếu chuỗi đạt độ dài `2 * n` thì đó là một tổ hợp hợp lệ.

**Mã nguồn Java:**
```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> result = new ArrayList<>();
        backtrack(result, new StringBuilder(), 0, 0, n);
        return result;
    }

    private void backtrack(List<String> result, StringBuilder current, int open, int close, int max) {
        if (current.length() == max * 2) {
            result.add(current.toString());
            return;
        }
        if (open < max) {
            current.append("(");
            backtrack(result, current, open + 1, close, max);
            current.deleteCharAt(current.length() - 1);
        }
        if (close < open) {
            current.append(")");
            backtrack(result, current, open, close + 1, max);
            current.deleteCharAt(current.length() - 1);
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(\frac{4^N}{\sqrt{N}})$, tiệm cận với số Catalan thứ $N$.
- Space Complexity: $O(N)$ (recursion stack và StringBuilder).

---

## 16. N-Queens (LeetCode 51)
**Đề bài chi tiết:** Bài toán đặt `n` quân hậu trên bàn cờ `n x n` sao cho không có 2 quân hậu nào tấn công nhau. Trả về tất cả các giải pháp phân bổ riêng biệt, `Q` đại diện quân hậu, `.` đại diện ô trống.

**Phân tích thuật toán:**
Duyệt qua từng hàng `row`. Tại mỗi hàng, duyệt qua từng cột `col`. Quân hậu an toàn nếu cột, chéo chính (`row - col`), chéo phụ (`row + col`) không bị chiếm. Dùng 3 mảng boolean để kiểm tra $O(1)$. Đặt `Q`, đệ quy, sau đó đổi lại thành `.` (backtrack).

**Mã nguồn Java:**
```java
class Solution {
    public List<List<String>> solveNQueens(int n) {
        List<List<String>> result = new ArrayList<>();
        char[][] board = new char[n][n];
        for (char[] row : board) Arrays.fill(row, '.');
        
        boolean[] cols = new boolean[n];
        boolean[] diag1 = new boolean[2 * n]; // row - col + n
        boolean[] diag2 = new boolean[2 * n]; // row + col
        
        backtrack(result, board, 0, n, cols, diag1, diag2);
        return result;
    }

    private void backtrack(List<List<String>> result, char[][] board, int row, int n, boolean[] cols, boolean[] diag1, boolean[] diag2) {
        if (row == n) {
            List<String> list = new ArrayList<>();
            for (char[] r : board) list.add(new String(r));
            result.add(list);
            return;
        }
        for (int col = 0; col < n; col++) {
            int d1 = row - col + n;
            int d2 = row + col;
            if (cols[col] || diag1[d1] || diag2[d2]) continue;
            
            board[row][col] = 'Q';
            cols[col] = diag1[d1] = diag2[d2] = true;
            
            backtrack(result, board, row + 1, n, cols, diag1, diag2);
            
            board[row][col] = '.';
            cols[col] = diag1[d1] = diag2[d2] = false;
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N!)$.
- Space Complexity: $O(N^2)$ (cho bàn cờ).

---

## 17. N-Queens II (LeetCode 52)
**Đề bài chi tiết:** Giống bài toán N-Queens, nhưng thay vì trả về cấu hình bàn cờ, bạn chỉ cần trả về số lượng giải pháp phân bổ quân hậu riêng biệt.

**Phân tích thuật toán:**
Sử dụng thuật toán tương tự N-Queens, nhưng không cần bảo trì ma trận `char[][]`. Khi một nhánh đệ quy đặt thành công `N` quân hậu (đạt đến `row == n`), ta chỉ cần tăng biến `count`.

**Mã nguồn Java:**
```java
class Solution {
    int count = 0;
    
    public int totalNQueens(int n) {
        boolean[] cols = new boolean[n];
        boolean[] diag1 = new boolean[2 * n];
        boolean[] diag2 = new boolean[2 * n];
        backtrack(0, n, cols, diag1, diag2);
        return count;
    }

    private void backtrack(int row, int n, boolean[] cols, boolean[] diag1, boolean[] diag2) {
        if (row == n) {
            count++;
            return;
        }
        for (int col = 0; col < n; col++) {
            int d1 = row - col + n;
            int d2 = row + col;
            if (cols[col] || diag1[d1] || diag2[d2]) continue;
            
            cols[col] = diag1[d1] = diag2[d2] = true;
            backtrack(row + 1, n, cols, diag1, diag2);
            cols[col] = diag1[d1] = diag2[d2] = false;
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N!)$.
- Space Complexity: $O(N)$ cho các mảng boolean.

---

## 18. Sudoku Solver (LeetCode 37)
**Đề bài chi tiết:** Viết chương trình giải bảng Sudoku. Điền `1-9` sao cho mỗi số xuất hiện duy nhất ở hàng, cột, và vùng `3x3`.

**Phân tích thuật toán:**
Duyệt qua từng ô, nếu gặp ô `'.'`, thử điền từ `1` đến `9`. Hàm `isValid` kiểm tra xem số điền có phá vỡ luật Sudoku không. Nếu không, tiến hành đệ quy giải tiếp. Nếu hàm đệ quy trả về `true`, quá trình hoàn tất. Nếu thử cả 9 số đều sai, trả về `false` để kích hoạt quay lui (backtrack).

**Mã nguồn Java:**
```java
class Solution {
    public void solveSudoku(char[][] board) {
        backtrack(board);
    }

    private boolean backtrack(char[][] board) {
        for (int r = 0; r < 9; r++) {
            for (int c = 0; c < 9; c++) {
                if (board[r][c] == '.') {
                    for (char k = '1'; k <= '9'; k++) {
                        if (isValid(board, r, c, k)) {
                            board[r][c] = k;
                            if (backtrack(board)) return true;
                            board[r][c] = '.';
                        }
                    }
                    return false;
                }
            }
        }
        return true;
    }

    private boolean isValid(char[][] board, int row, int col, char c) {
        for (int i = 0; i < 9; i++) {
            if (board[row][i] == c) return false;
            if (board[i][col] == c) return false;
            if (board[3 * (row / 3) + i / 3][3 * (col / 3) + i % 3] == c) return false;
        }
        return true;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(9^M)$ với $M$ là số ô trống.
- Space Complexity: $O(1)$ (call stack sâu tối đa 81).

---

## 19. Valid Sudoku (LeetCode 36)
**Đề bài chi tiết:** Xác định bảng Sudoku `9x9` hiện tại có hợp lệ không. Chỉ đánh giá những ô đã điền.

**Phân tích thuật toán:**
Sử dụng HashSet để theo dõi các số đã thấy. Lưu ý mã hóa chuỗi nhận diện để phân biệt số ở hàng, cột và khối `3x3`. Cụ thể, định dạng chuỗi `"số ở vị trí tương ứng"`. Thuật toán này không hẳn là backtracking nhưng là nền tảng cắt tỉa của bài Sudoku.

**Mã nguồn Java:**
```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        Set<String> seen = new HashSet<>();
        for (int i = 0; i < 9; ++i) {
            for (int j = 0; j < 9; ++j) {
                char number = board[i][j];
                if (number != '.') {
                    // HashSet.add() trả về false nếu phần tử đã tồn tại
                    if (!seen.add(number + " in row " + i) ||
                        !seen.add(number + " in col " + j) ||
                        !seen.add(number + " in block " + i / 3 + "-" + j / 3)) {
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
- Time Complexity: $O(1)$ vì bảng kích thước luôn là `9x9`.
- Space Complexity: $O(1)$ HashSet lưu tối đa 243 chuỗi.

---

## 20. Word Search (LeetCode 79)
**Đề bài chi tiết:** Cho lưới `m x n` chứa các ký tự và một chuỗi `word`, trả về `true` nếu `word` tồn tại trong lưới bằng cách đi ngang hoặc dọc, không dùng lại ô đã đi qua.

**Phân tích thuật toán:**
Duyệt tất cả các vị trí trong lưới làm điểm bắt đầu. Hàm `backtrack` đệ quy theo 4 hướng. Để đánh dấu ô đã thăm, thay thế tạm thời bằng một ký tự đặc biệt (ví dụ `#`). Quay lui (backtrack) sẽ trả lại giá trị ban đầu nếu nhánh thất bại.

**Mã nguồn Java:**
```java
class Solution {
    public boolean exist(char[][] board, String word) {
        int m = board.length, n = board[0].length;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == word.charAt(0) && backtrack(board, word, i, j, 0)) {
                    return true;
                }
            }
        }
        return false;
    }

    private boolean backtrack(char[][] board, String word, int r, int c, int index) {
        if (index == word.length()) return true;
        if (r < 0 || r >= board.length || c < 0 || c >= board[0].length || board[r][c] != word.charAt(index)) {
            return false;
        }

        char temp = board[r][c];
        board[r][c] = '#'; // Mark as visited
        
        boolean found = backtrack(board, word, r + 1, c, index + 1) ||
                        backtrack(board, word, r - 1, c, index + 1) ||
                        backtrack(board, word, r, c + 1, index + 1) ||
                        backtrack(board, word, r, c - 1, index + 1);
        
        board[r][c] = temp; // Backtrack
        return found;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(M \times N \times 3^L)$ với $L$ là độ dài `word` (3 hướng đi ở các bước tiếp theo).
- Space Complexity: $O(L)$ cho call stack đệ quy.

---

## 21. Word Search II (LeetCode 212)
**Đề bài chi tiết:** Cho một bảng ký tự `m x n` (board) và một danh sách các từ `words`, hãy trả về tất cả các từ có trên bảng. Các từ phải được tạo thành từ các ký tự kề nhau theo chiều dọc hoặc ngang, trong đó cùng một ô chứa ký tự không thể được sử dụng nhiều hơn một lần trong một từ.

**Phân tích thuật toán:**
Thay vì tìm từng từ một bằng Backtracking (sẽ bị TLE), ta xây dựng một cây tiền tố (Trie) chứa tất cả các từ trong `words`. Duyệt bảng board bằng DFS/Backtracking, tại mỗi bước di chuyển, đồng thời duyệt trên Trie. Khi chạm đến nút lá của Trie (tức là hoàn thành một từ), lưu vào kết quả và xóa từ đó khỏi Trie để tránh bị lặp.

**Mã nguồn Java:**
```java
class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        String word;
    }

    public List<String> findWords(char[][] board, String[] words) {
        List<String> res = new ArrayList<>();
        TrieNode root = buildTrie(words);
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                dfs(board, i, j, root, res);
            }
        }
        return res;
    }

    private void dfs(char[][] board, int i, int j, TrieNode p, List<String> res) {
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length || board[i][j] == '#' || p.children[board[i][j] - 'a'] == null) return;
        
        char c = board[i][j];
        p = p.children[c - 'a'];
        if (p.word != null) {
            res.add(p.word);
            p.word = null;     // Khử duplicate và tối ưu Trie
        }

        board[i][j] = '#'; // visited
        dfs(board, i - 1, j, p, res);
        dfs(board, i + 1, j, p, res);
        dfs(board, i, j - 1, p, res);
        dfs(board, i, j + 1, p, res);
        board[i][j] = c;   // backtrack
    }

    private TrieNode buildTrie(String[] words) {
        TrieNode root = new TrieNode();
        for (String w : words) {
            TrieNode p = root;
            for (char c : w.toCharArray()) {
                int i = c - 'a';
                if (p.children[i] == null) p.children[i] = new TrieNode();
                p = p.children[i];
            }
            p.word = w;
        }
        return root;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(M \times N \times 4 \times 3^{L-1})$ với $M \times N$ là kích thước lưới, $L$ là độ dài tối đa của từ.
- Space Complexity: $O(W \times L)$ cho cấu trúc Trie, với $W$ là số từ và $L$ là độ dài trung bình.

---

## 22. Find Unique Binary String (LeetCode 1980)
**Đề bài chi tiết:** Cho một mảng các chuỗi nhị phân `nums` có độ dài `n`, trong đó `nums` có đúng `n` chuỗi duy nhất. Hãy trả về một chuỗi nhị phân có độ dài `n` không xuất hiện trong `nums`. Nếu có nhiều đáp án, trả về đáp án nào cũng được.

**Phân tích thuật toán:**
Bài này có thể giải bằng cách duyệt Cantor's diagonal argument trong $O(N)$ nhưng ở phạm trù Backtracking, ta đệ quy từng ký tự ('0' hoặc '1') để xây dựng chuỗi `n` bit, kiểm tra xem nó có nằm trong danh sách không (dùng HashSet).

**Mã nguồn Java:**
```java
class Solution {
    public String findDifferentBinaryString(String[] nums) {
        Set<String> set = new HashSet<>();
        for (String num : nums) set.add(num);
        return backtrack(new StringBuilder(), nums.length, set);
    }

    private String backtrack(StringBuilder current, int n, Set<String> set) {
        if (current.length() == n) {
            String s = current.toString();
            return set.contains(s) ? null : s;
        }

        current.append('0');
        String res = backtrack(current, n, set);
        if (res != null) return res;
        current.deleteCharAt(current.length() - 1);

        current.append('1');
        res = backtrack(current, n, set);
        if (res != null) return res;
        current.deleteCharAt(current.length() - 1);

        return null;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(2^N)$ tồi tệ nhất nếu duyệt Backtracking (nhưng thực tế gặp đáp án rất sớm). Cách Cantor $O(N)$.
- Space Complexity: $O(N)$ (cho Set và Stack).

---

## 23. Partition to K Equal Sum Subsets (LeetCode 698)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` và số nguyên `k`, hãy xác định xem có thể chia mảng thành `k` tập con không rỗng có tổng các phần tử bằng nhau hay không.

**Phân tích thuật toán:**
Tính tổng toàn mảng, nếu không chia hết cho $k$ thì sai. Tính $target = \text{sum} / k$. Sắp xếp mảng giảm dần để tối ưu thời gian (pruning). Dùng backtracking: duyệt các tập con 1 đến $k$, cố gắng bỏ phần tử hiện tại vào 1 trong $k$ tập, nếu không làm vỡ target thì đệ quy phần tử kế.

**Mã nguồn Java:**
```java
class Solution {
    public boolean canPartitionKSubsets(int[] nums, int k) {
        int sum = 0;
        for (int n : nums) sum += n;
        if (sum % k != 0) return false;
        int target = sum / k;
        
        Arrays.sort(nums);
        // Sắp xếp giảm dần để Prune nhanh hơn (chèn các số lớn trước)
        for (int i = 0, j = nums.length - 1; i < j; i++, j--) {
            int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
        }
        
        int[] buckets = new int[k];
        return backtrack(nums, 0, buckets, target);
    }

    private boolean backtrack(int[] nums, int index, int[] buckets, int target) {
        if (index == nums.length) return true;
        
        for (int i = 0; i < buckets.length; i++) {
            if (buckets[i] + nums[index] <= target) {
                buckets[i] += nums[index];
                if (backtrack(nums, index + 1, buckets, target)) return true;
                buckets[i] -= nums[index];
            }
            // Tối ưu cắt tỉa: Nếu bucket trống đầu tiên không thỏa mãn, các bucket trống sau cũng không.
            if (buckets[i] == 0) break;
        }
        return false;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(K^N)$ vì có $N$ phần tử, mỗi phần tử có $K$ lựa chọn bucket.
- Space Complexity: $O(N + K)$ do call stack và mảng buckets.

---

## 24. Matchsticks to Square (LeetCode 473)
**Đề bài chi tiết:** Bạn được cho mảng `matchsticks` chứa độ dài các que diêm. Bạn cần dùng tất cả các que diêm để ghép thành một hình vuông. Không được bẻ gãy bất kỳ que diêm nào.

**Phân tích thuật toán:**
Bài này y hệt bài 23 "Partition to K Equal Sum Subsets", với $k = 4$. Thuật toán hoàn toàn giống, ta sắp xếp độ dài diêm giảm dần để giảm số nhánh cần kiểm tra. Nếu tổng các que diêm không chia hết cho 4 thì trả về false.

**Mã nguồn Java:**
```java
class Solution {
    public boolean makesquare(int[] matchsticks) {
        int sum = 0;
        for (int num : matchsticks) sum += num;
        if (sum == 0 || sum % 4 != 0) return false;
        
        Arrays.sort(matchsticks);
        for (int i = 0, j = matchsticks.length - 1; i < j; i++, j--) {
            int temp = matchsticks[i]; matchsticks[i] = matchsticks[j]; matchsticks[j] = temp;
        }
        
        return backtrack(matchsticks, 0, new int[4], sum / 4);
    }

    private boolean backtrack(int[] matchsticks, int index, int[] sides, int target) {
        if (index == matchsticks.length) {
            return sides[0] == target && sides[1] == target && sides[2] == target;
        }
        for (int i = 0; i < 4; i++) {
            if (sides[i] + matchsticks[index] <= target) {
                sides[i] += matchsticks[index];
                if (backtrack(matchsticks, index + 1, sides, target)) return true;
                sides[i] -= matchsticks[index];
            }
            if (sides[i] == 0) break; // Pruning
        }
        return false;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(4^N)$. Cắt tỉa (Pruning) sẽ giảm đáng kể số lượng cấu hình kiểm tra trong thực tế.
- Space Complexity: $O(N)$ độ sâu Stack.

---

## 25. Splitting a String Into Descending Consecutive Values (LeetCode 1849)
**Đề bài chi tiết:** Bạn được cho một chuỗi số `s`. Kiểm tra xem có thể tách `s` thành hai hay nhiều phần sao cho các phần này có giá trị số nguyên giảm dần và chênh lệch liên tiếp bằng 1 hay không.

**Phân tích thuật toán:**
Thử chia chuỗi thành phần tử đầu tiên, rồi dùng Backtracking kiểm tra xem phần còn lại có khớp với điều kiện "số sau nhỏ hơn số trước đúng 1 đơn vị" hay không. Lưu ý xử lý giá trị số vượt qua kiểu dữ liệu thông thường, ta nên dùng `long` cẩn thận để chống tràn hoặc thoát sớm khi vượt quá giới hạn.

**Mã nguồn Java:**
```java
class Solution {
    public boolean splitString(String s) {
        return backtrack(s, 0, null, 0);
    }
    
    private boolean backtrack(String s, int index, Long prev, int count) {
        if (index == s.length()) {
            return count >= 2;
        }
        
        long num = 0;
        for (int i = index; i < s.length(); i++) {
            // Ngăn chặn tràn số Long
            if (num > Long.MAX_VALUE / 10) break; 
            num = num * 10 + (s.charAt(i) - '0');
            
            if (prev == null || prev - num == 1) {
                if (backtrack(s, i + 1, num, count + 1)) {
                    return true;
                }
            }
        }
        return false;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N^2)$ (mặc dù là Backtracking nhưng nhánh đúng chỉ tồn tại rất ít do điều kiện nghiêm ngặt).
- Space Complexity: $O(N)$ gọi hàm đệ quy.

---

## 26. Letter Combinations of a Phone Number (LeetCode 17)
**Đề bài chi tiết:** Cho chuỗi chữ số từ `2-9` tượng trưng cho phím trên điện thoại, trả về tất cả các tổ hợp ký tự có thể có.

**Phân tích thuật toán:**
Đầu tiên cần tạo bảng băm ánh xạ giữa các phím số (2-9) và các ký tự ('abc', 'def', ...). Sau đó sử dụng DFS Backtracking để duyệt từng phím, với mỗi phím ta lặp qua danh sách ký tự tương ứng và gọi đệ quy xử lý phím tiếp theo.

**Mã nguồn Java:**
```java
class Solution {
    private static final String[] MAP = {
        "", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"
    };

    public List<String> letterCombinations(String digits) {
        List<String> result = new ArrayList<>();
        if (digits == null || digits.length() == 0) return result;
        backtrack(result, new StringBuilder(), digits, 0);
        return result;
    }

    private void backtrack(List<String> result, StringBuilder current, String digits, int index) {
        if (index == digits.length()) {
            result.add(current.toString());
            return;
        }
        String letters = MAP[digits.charAt(index) - '0'];
        for (char c : letters.toCharArray()) {
            current.append(c);
            backtrack(result, current, digits, index + 1);
            current.deleteCharAt(current.length() - 1);
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(4^N \times N)$ với $N$ là số lượng chữ số (vì phím '7', '9' có 4 ký tự).
- Space Complexity: $O(N)$ depth của recursion stack.

---

## 27. Restore IP Addresses (LeetCode 93)
**Đề bài chi tiết:** Cho chuỗi `s` chứa các chữ số, hãy trả về tất cả địa chỉ IPv4 hợp lệ có thể được tạo ra bằng cách chèn dấu `.`.

**Phân tích thuật toán:**
Một IP hợp lệ có 4 phần tử (mỗi phần tử $\le 255$ và không có số 0 đứng đầu nếu có trên 1 chữ số). Bằng Backtracking, tại mỗi vị trí, ta lấy 1, 2, hoặc 3 chữ số để làm một phần IP. Nếu đúng luật, ta đi tiếp. Nếu ta đã lấy đủ 4 phần và hết chuỗi, ta lưu vào danh sách.

**Mã nguồn Java:**
```java
class Solution {
    public List<String> restoreIpAddresses(String s) {
        List<String> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(), s, 0);
        return result;
    }

    private void backtrack(List<String> result, List<String> path, String s, int index) {
        if (path.size() == 4) {
            if (index == s.length()) {
                result.add(String.join(".", path));
            }
            return;
        }
        for (int i = 1; i <= 3 && index + i <= s.length(); i++) {
            String part = s.substring(index, index + i);
            if (isValid(part)) {
                path.add(part);
                backtrack(result, path, s, index + i);
                path.remove(path.size() - 1);
            }
        }
    }

    private boolean isValid(String s) {
        if (s.length() > 1 && s.charAt(0) == '0') return false;
        int val = Integer.parseInt(s);
        return val >= 0 && val <= 255;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(3^4) = O(1)$ với độ sâu 4 tầng nhánh tối đa không đổi.
- Space Complexity: $O(1)$ stack luôn dưới 4 cấp.

---

## 28. Optimal Account Balancing (LeetCode 465)
**Đề bài chi tiết:** Cho danh sách các giao dịch (ai nợ ai bao nhiêu), trả về số lượng giao dịch tối thiểu để xóa hết nợ.

**Phân tích thuật toán:**
Tính số dư ròng của mỗi người. Lọc bỏ các số dư 0. Bài toán trở thành: ghép các số dư bù trừ cho nhau với số bước ghép tối thiểu. Sử dụng đệ quy (Backtracking). Với số dư khác 0 hiện tại `balances[start]`, duyệt các phần tử `i` phía sau trái dấu, tiến hành "trả tiền", đệ quy, sau đó quay lui khôi phục trạng thái cũ.

**Mã nguồn Java:**
```java
class Solution {
    public int minTransfers(int[][] transactions) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int[] t : transactions) {
            map.put(t[0], map.getOrDefault(t[0], 0) - t[2]);
            map.put(t[1], map.getOrDefault(t[1], 0) + t[2]);
        }
        List<Integer> list = new ArrayList<>();
        for (int val : map.values()) {
            if (val != 0) list.add(val);
        }
        int[] balances = list.stream().mapToInt(i -> i).toArray();
        return backtrack(balances, 0);
    }

    private int backtrack(int[] balances, int start) {
        while (start < balances.length && balances[start] == 0) {
            start++;
        }
        if (start == balances.length) return 0;
        
        int min = Integer.MAX_VALUE;
        for (int i = start + 1; i < balances.length; i++) {
            // Tối ưu: Chỉ ghép với số trái dấu
            if (balances[i] * balances[start] < 0) {
                balances[i] += balances[start]; // Chuyển tiền từ start sang i
                min = Math.min(min, 1 + backtrack(balances, start + 1));
                balances[i] -= balances[start]; // Quay lui
            }
        }
        return min;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N!)$ trong trường hợp tồi tệ nhất. Vì bài toán giới hạn số người giao dịch $N \le 12$, nên thuật toán hoạt động tốt.
- Space Complexity: $O(N)$ depth của hàm gọi đệ quy.

---

## 29. Robot Room Cleaner (LeetCode 427)
**Đề bài chi tiết:** Bạn điều khiển một con robot để quét dọn tất cả các ô trên ma trận. Không biết kích thước, chỉ biết hàm `move()`, `turnLeft()`, `turnRight()`, `clean()`.

**Phân tích thuật toán:**
Đặt tọa độ ban đầu của robot là `(0, 0)` và hướng đi là mảng `directions`. DFS bằng đệ quy. Khi thăm ô, `clean` nó và lưu vào mảng `visited` (bằng HashSet + tọa độ chuỗi). Sau khi thăm hết 4 hướng, cần dùng hàm Backtrack (quay lưng lại `turnRight` 2 lần, lùi về `move`, quay lại trạng thái ban đầu `turnRight` 2 lần) để lùi robot về vị trí ô trước.

**Mã nguồn Java:**
```java
/**
 * // This is the robot's control interface.
 * interface Robot {
 *     // Returns true if the cell in front is open and robot moves into the cell.
 *     public boolean move();
 *     public void turnLeft();
 *     public void turnRight();
 *     public void clean();
 * }
 */
class Solution {
    int[][] dirs = {{-1, 0}, {0, 1}, {1, 0}, {0, -1}}; // Lên, Phải, Xuống, Trái
    Set<String> visited = new HashSet<>();

    public void cleanRoom(Robot robot) {
        backtrack(robot, 0, 0, 0);
    }

    private void backtrack(Robot robot, int row, int col, int dir) {
        visited.add(row + "," + col);
        robot.clean();

        for (int i = 0; i < 4; i++) {
            int newDir = (dir + i) % 4;
            int newRow = row + dirs[newDir][0];
            int newCol = col + dirs[newDir][1];

            if (!visited.contains(newRow + "," + newCol) && robot.move()) {
                backtrack(robot, newRow, newCol, newDir);
                goBack(robot); // Quay lui không gian
            }
            robot.turnRight(); // Đổi sang hướng kiểm tra tiếp
        }
    }

    private void goBack(Robot robot) {
        robot.turnRight();
        robot.turnRight();
        robot.move();
        robot.turnRight();
        robot.turnRight();
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(V + E) = O(N)$ với $N$ là tổng số ô trống trên ma trận cần quét.
- Space Complexity: $O(N)$ (lưu Set tọa độ đã thăm).

---

## 30. Non-decreasing Subsequences (LeetCode 491)
**Đề bài chi tiết:** Cho một mảng `nums`, trả về tất cả dãy con có độ dài ít nhất là 2 mà theo thứ tự tăng dần (không giảm). Trả về dưới bất kỳ thứ tự nào.

**Phân tích thuật toán:**
Bài toán subset bình thường nhưng có 2 biến thể: dãy con phải thỏa mãn phần tử trước $\le$ phần tử sau, và kết quả phải độc nhất. Vì ta không được phép làm hỏng thứ tự ban đầu bằng cách sắp xếp, ta dùng Backtracking nhưng ở mỗi level đệ quy ta dùng một `HashSet` lưu các số đã chọn trên các nhánh con tương đương để bỏ qua trùng lặp.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> findSubsequences(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(), nums, 0);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> temp, int[] nums, int start) {
        if (temp.size() > 1) {
            result.add(new ArrayList<>(temp));
        }
        
        Set<Integer> used = new HashSet<>(); // Set cho mỗi level đệ quy
        for (int i = start; i < nums.length; i++) {
            if (used.contains(nums[i])) continue;
            if (temp.isEmpty() || nums[i] >= temp.get(temp.size() - 1)) {
                used.add(nums[i]);
                temp.add(nums[i]);
                backtrack(result, temp, nums, i + 1);
                temp.remove(temp.size() - 1);
            }
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(2^N)$ trong trường hợp tồi tệ, tuy có sử dụng prune nhánh.
- Space Complexity: $O(N)$ độ sâu của đệ quy và mỗi hàm tạo một set nội bộ.
