# 04. Bài tập Greedy on Strings (Tham lam trên Chuỗi)

## 1. Reorganize String
**Đề bài chi tiết:** Cho một chuỗi `s`, hãy sắp xếp lại các ký tự của chuỗi sao cho không có 2 ký tự kề nhau nào giống nhau. Nếu có thể, trả về bất kỳ chuỗi hợp lệ nào. Nếu không thể, trả về chuỗi rỗng `""`.
**Phân tích thuật toán:**
Đếm tần suất các ký tự. Nếu một ký tự xuất hiện nhiều hơn `(N + 1) / 2` lần, thì không thể sắp xếp được. Dùng Max Heap (Priority Queue) ưu tiên lấy 2 ký tự có tần suất cao nhất ra ghép vào chuỗi kết quả, sau đó giảm tần suất đi 1 và thêm lại vào Heap nếu tần suất còn $> 0$.
**Mã nguồn Java:**
```java
class Solution {
    public String reorganizeString(String s) {
        int[] count = new int[26];
        for (char c : s.toCharArray()) count[c - 'a']++;
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> b[1] - a[1]);
        for (int i = 0; i < 26; i++) {
            if (count[i] > 0) {
                if (count[i] > (s.length() + 1) / 2) return "";
                pq.offer(new int[]{i, count[i]});
            }
        }
        StringBuilder sb = new StringBuilder();
        while (pq.size() >= 2) {
            int[] first = pq.poll();
            int[] second = pq.poll();
            sb.append((char) (first[0] + 'a'));
            sb.append((char) (second[0] + 'a'));
            first[1]--; second[1]--;
            if (first[1] > 0) pq.offer(first);
            if (second[1] > 0) pq.offer(second);
        }
        if (!pq.isEmpty()) {
            sb.append((char) (pq.poll()[0] + 'a'));
        }
        return sb.toString();
    }
}
```
**Độ phức tạp:** Thời gian $O(N \log K)$ với $K = 26$ nên là $O(N)$. Không gian $O(K) = O(1)$.

## 2. Minimum Add to Make Parentheses Valid
**Đề bài chi tiết:** Cho chuỗi `s` chỉ chứa `'('` và `')'`, trả về số lượng ít nhất các dấu ngoặc cần thêm vào bất kỳ vị trí nào để chuỗi trở thành chuỗi ngoặc hợp lệ.
**Phân tích thuật toán:**
Dùng 2 biến đếm: `open` đếm số lượng dấu ngoặc mở `'('` đang cần được đóng, và `close` đếm số lượng dấu ngoặc đóng `')'` thừa không có ngoặc mở tương ứng. Duyệt chuỗi bằng Greedy để tăng/giảm hai biến này.
**Mã nguồn Java:**
```java
class Solution {
    public int minAddToMakeValid(String s) {
        int open = 0, close = 0;
        for (char c : s.toCharArray()) {
            if (c == '(') {
                open++;
            } else {
                if (open > 0) open--;
                else close++;
            }
        }
        return open + close;
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$.

## 3. Partition Labels
**Đề bài chi tiết:** Cho chuỗi `s` (chỉ viết thường). Cần chia chuỗi thành nhiều phần nhất có thể sao cho mỗi ký tự xuất hiện trong nhiều nhất một phần. Trả về một mảng chứa kích thước của các phần đó.
**Phân tích thuật toán:**
Đầu tiên duyệt qua chuỗi để lưu vị trí xuất hiện cuối cùng của mỗi ký tự. Lần duyệt thứ hai, cập nhật vị trí lớn nhất (`end`) mà ta phải duyệt đến. Khi chỉ số hiện tại `i == end`, ta hoàn thành một phần.
**Mã nguồn Java:**
```java
class Solution {
    public List<Integer> partitionLabels(String s) {
        int[] last = new int[26];
        for (int i = 0; i < s.length(); ++i) {
            last[s.charAt(i) - 'a'] = i;
        }
        List<Integer> ans = new ArrayList<>();
        int start = 0, end = 0;
        for (int i = 0; i < s.length(); ++i) {
            end = Math.max(end, last[s.charAt(i) - 'a']);
            if (i == end) {
                ans.add(i - start + 1);
                start = i + 1;
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$.

## 4. Break a Palindrome
**Đề bài chi tiết:** Cho một chuỗi palindrome, hãy thay thế đúng một ký tự bằng ký tự thường bất kỳ để chuỗi mới không còn là palindrome và có thứ tự từ điển nhỏ nhất có thể. Trả về chuỗi rỗng nếu không thể.
**Phân tích thuật toán:**
Chuỗi độ dài 1 không thể. Duyệt qua nửa đầu của chuỗi, nếu ký tự khác `'a'`, ta đổi nó thành `'a'` là có kết quả nhỏ nhất (Greedy). Nếu toàn bộ nửa đầu là `'a'` (ví dụ `aba`), ta bắt buộc phải đổi ký tự cuối cùng thành `'b'`.
**Mã nguồn Java:**
```java
class Solution {
    public String breakPalindrome(String palindrome) {
        int n = palindrome.length();
        if (n <= 1) return "";
        char[] arr = palindrome.toCharArray();
        for (int i = 0; i < n / 2; i++) {
            if (arr[i] != 'a') {
                arr[i] = 'a';
                return new String(arr);
            }
        }
        arr[n - 1] = 'b';
        return new String(arr);
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(N)$.

## 5. Minimum Number of Swaps to Make the String Balanced
**Đề bài chi tiết:** Cho chuỗi chứa dấu ngoặc vuông `[` và `]`. Tìm số lượng lần hoán đổi vị trí (swap) tối thiểu để dãy ngoặc trở nên hợp lệ.
**Phân tích thuật toán:**
Ta loại bỏ các ngoặc đã hợp lệ (ghép cặp `[]`). Cuối cùng ta sẽ còn lại dạng `]]]...[[[`. Số lượng ngoặc không hợp lệ là `mismatch`. Mỗi lần swap có thể giải quyết được 2 mismatch. Do đó, số lần swap là `(mismatch + 1) / 2`.
**Mã nguồn Java:**
```java
class Solution {
    public int minSwaps(String s) {
        int mismatch = 0;
        for (char c : s.toCharArray()) {
            if (c == '[') {
                mismatch++;
            } else {
                if (mismatch > 0) {
                    mismatch--;
                }
            }
        }
        return (mismatch + 1) / 2;
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$.

## 6. Valid Parenthesis String
**Đề bài chi tiết:** Cho chuỗi `s` chứa `(`, `)` và `*`. Ký tự `*` có thể coi là `(`, hoặc `)` hoặc khoảng trắng. Kiểm tra xem chuỗi có hợp lệ hay không.
**Phân tích thuật toán:**
Dùng hai biến đếm `cmin` và `cmax` biểu diễn số lượng khoảng dư mở ngoặc tối thiểu và tối đa.
- Nếu là `(`, cả hai cùng tăng 1.
- Nếu là `)`, cả hai giảm 1.
- Nếu là `*`, `cmax` tăng 1 (coi như `(`), `cmin` giảm 1 (coi như `)`).
Nếu `cmax < 0`, ngay lập tức sai. Ép `cmin = Math.max(cmin, 0)`. Kết thúc vòng lặp, `cmin` phải bằng 0.
**Mã nguồn Java:**
```java
class Solution {
    public boolean checkValidString(String s) {
        int cmin = 0, cmax = 0;
        for (char c : s.toCharArray()) {
            if (c == '(') {
                cmax++; cmin++;
            } else if (c == ')') {
                cmax--; cmin--;
            } else {
                cmax++; cmin--;
            }
            if (cmax < 0) return false;
            cmin = Math.max(cmin, 0);
        }
        return cmin == 0;
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$.

## 7. Longest Palindrome
**Đề bài chi tiết:** Cho chuỗi `s`, tính độ dài dài nhất của một palindrome có thể được tạo ra bằng các chữ cái của `s`.
**Phân tích thuật toán:**
Đếm số lần xuất hiện của các ký tự. Với mỗi ký tự, ta có thể dùng phần chẵn của nó (ví dụ 5 dùng 4). Nếu có bất kỳ ký tự nào xuất hiện với số lần lẻ, ta có thể đặt 1 ký tự lẻ vào chính giữa palindrome.
**Mã nguồn Java:**
```java
class Solution {
    public int longestPalindrome(String s) {
        int[] count = new int[128];
        for (char c : s.toCharArray()) count[c]++;
        int length = 0;
        boolean hasOdd = false;
        for (int c : count) {
            length += c / 2 * 2;
            if (c % 2 == 1) hasOdd = true;
        }
        return hasOdd ? length + 1 : length;
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$.

## 8. Minimum Deletions to Make Character Frequencies Unique
**Đề bài chi tiết:** Cho chuỗi `s`, xóa ít ký tự nhất sao cho tần suất của mọi ký tự còn lại đều khác nhau (unique).
**Phân tích thuật toán:**
Đếm tần suất, sau đó dùng `HashSet` để theo dõi các tần suất đã được sử dụng. Nếu tần suất của ký tự đang xét đã tồn tại, ta giảm nó đi 1 (tức là xóa ký tự) cho đến khi không trùng hoặc về 0.
**Mã nguồn Java:**
```java
class Solution {
    public int minDeletions(String s) {
        int[] count = new int[26];
        for (char c : s.toCharArray()) count[c - 'a']++;
        Set<Integer> usedFreq = new HashSet<>();
        int deletions = 0;
        for (int freq : count) {
            while (freq > 0 && usedFreq.contains(freq)) {
                freq--;
                deletions++;
            }
            usedFreq.add(freq);
        }
        return deletions;
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$.

## 9. Construct K Palindrome Strings
**Đề bài chi tiết:** Cho chuỗi `s` và số `k`, kiểm tra xem có thể dùng toàn bộ ký tự của `s` để tạo thành đúng `k` chuỗi palindrome hay không.
**Phân tích thuật toán:**
Nếu `k > s.length()`, trả về `false`.
Đếm số lượng các ký tự có số lần xuất hiện lẻ. Mỗi ký tự lẻ bắt buộc phải là tâm của một chuỗi palindrome riêng biệt. Do đó, nếu số lượng ký tự lẻ > `k`, ta không thể tạo được. Ngược lại, ta có thể (vì các ký tự chẵn hoặc có thể tách ra dễ dàng để điền vào các palindrome).
**Mã nguồn Java:**
```java
class Solution {
    public boolean canConstruct(String s, int k) {
        if (s.length() < k) return false;
        int[] count = new int[26];
        for (char c : s.toCharArray()) count[c - 'a']++;
        int oddCount = 0;
        for (int c : count) {
            if (c % 2 == 1) oddCount++;
        }
        return oddCount <= k;
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$.

## 10. Largest Merge Of Two Strings
**Đề bài chi tiết:** Cho hai chuỗi `word1` và `word2`, trộn chúng bằng cách lấy ký tự từ đầu chuỗi này hoặc chuỗi kia để tạo chuỗi kết quả có thứ tự từ điển lớn nhất có thể.
**Phân tích thuật toán:**
Tại mỗi bước, ta so sánh chuỗi còn lại của `word1` và `word2`. Cắt ký tự đầu tiên của chuỗi nào (với phần còn lại) có thứ tự từ điển lớn hơn để đưa vào kết quả.
**Mã nguồn Java:**
```java
class Solution {
    public String largestMerge(String word1, String word2) {
        StringBuilder sb = new StringBuilder();
        int i = 0, j = 0;
        while (i < word1.length() && j < word2.length()) {
            if (word1.substring(i).compareTo(word2.substring(j)) >= 0) {
                sb.append(word1.charAt(i++));
            } else {
                sb.append(word2.charAt(j++));
            }
        }
        sb.append(word1.substring(i));
        sb.append(word2.substring(j));
        return sb.toString();
    }
}
```
**Độ phức tạp:** Thời gian $O(N^2)$ trong trường hợp tệ (so sánh `substring`). Có thể tối ưu thành $O(N)$ bằng Suffix Array hoặc dùng Two-pointers cẩn thận.

## 11. Remove K Digits
**Đề bài chi tiết:** Cho chuỗi `num` đại diện cho một số nguyên không âm và một số nguyên `k`. Hãy xóa `k` chữ số khỏi `num` sao cho số nguyên tạo thành là nhỏ nhất có thể.
**Phân tích thuật toán:**
Sử dụng Stack (hoặc StringBuilder đóng vai trò như Stack). Duyệt qua từng chữ số của `num`. Nếu chữ số hiện tại nhỏ hơn chữ số ở đỉnh Stack và ta vẫn còn quyền xóa (`k > 0`), ta sẽ xóa chữ số ở đỉnh Stack. Sau đó đẩy chữ số hiện tại vào Stack. Lưu ý không đẩy số `0` nếu Stack đang rỗng (để tránh số 0 ở đầu). Nếu kết thúc vòng lặp mà vẫn còn `k > 0`, ta tiếp tục xóa các chữ số ở cuối Stack.
**Mã nguồn Java:**
```java
class Solution {
    public String removeKdigits(String num, int k) {
        StringBuilder sb = new StringBuilder();
        for (char c : num.toCharArray()) {
            while (k > 0 && sb.length() > 0 && sb.charAt(sb.length() - 1) > c) {
                sb.deleteCharAt(sb.length() - 1);
                k--;
            }
            if (sb.length() > 0 || c != '0') {
                sb.append(c);
            }
        }
        while (k > 0 && sb.length() > 0) {
            sb.deleteCharAt(sb.length() - 1);
            k--;
        }
        return sb.length() == 0 ? "0" : sb.toString();
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(N)$ với $N$ là độ dài chuỗi `num`.

## 12. Lexicographically Smallest String After Applying Operations
**Đề bài chi tiết:** Cho chuỗi `s` gồm các chữ số chẵn, cùng 2 phép toán: (1) Cộng `a` vào tất cả các chữ số ở vị trí lẻ (chu trình 0-9), (2) Dịch phải xoay vòng chuỗi đi `b` vị trí. Tìm chuỗi có thứ tự từ điển nhỏ nhất sau khi áp dụng các phép toán bất kỳ số lần nào.
**Phân tích thuật toán:**
Sử dụng thuật toán duyệt đồ thị BFS (hoặc DFS). Lưu lại các trạng thái chuỗi đã sinh ra vào `HashSet` để tránh lặp vô hạn (vì số lượng trạng thái là hữu hạn). Từ một chuỗi, tạo 2 chuỗi mới tương ứng với 2 phép toán và tiếp tục duyệt. Cập nhật chuỗi nhỏ nhất từng thấy trong quá trình duyệt.
**Mã nguồn Java:**
```java
class Solution {
    public String findLexSmallestString(String s, int a, int b) {
        String res = s;
        Queue<String> q = new LinkedList<>();
        Set<String> seen = new HashSet<>();
        q.offer(s);
        seen.add(s);
        
        while (!q.isEmpty()) {
            String curr = q.poll();
            if (curr.compareTo(res) < 0) {
                res = curr;
            }
            
            // Thao tác 1: Cộng 'a' vào các vị trí lẻ
            char[] arr = curr.toCharArray();
            for (int i = 1; i < arr.length; i += 2) {
                arr[i] = (char) (((arr[i] - '0' + a) % 10) + '0');
            }
            String added = new String(arr);
            if (seen.add(added)) q.offer(added);
            
            // Thao tác 2: Dịch xoay vòng 'b' vị trí
            String rotated = curr.substring(curr.length() - b) + curr.substring(0, curr.length() - b);
            if (seen.add(rotated)) q.offer(rotated);
        }
        return res;
    }
}
```
**Độ phức tạp:** Thời gian $O(10 \times 10 \times N)$ do số lượng trạng thái bị giới hạn, Không gian $O(N \times \text{trạng thái})$.

## 13. Minimum Time to Make Rope Colorful
**Đề bài chi tiết:** Có `n` quả bóng xếp thành hàng (chuỗi `colors`) và mảng `neededTime` biểu thị thời gian để loại bỏ mỗi quả bóng. Không được để 2 quả bóng liền kề cùng màu. Tính thời gian tối thiểu để làm cho hàng bóng trở nên hợp lệ.
**Phân tích thuật toán:**
Dùng kỹ thuật tham lam: Khi có một nhóm các quả bóng kề nhau có cùng màu, ta phải loại bỏ tất cả ngoại trừ đúng 1 quả bóng. Để tối ưu (tổng thời gian loại bỏ là tối thiểu), ta giữ lại quả bóng có thời gian loại bỏ lớn nhất trong nhóm. Chi phí bị loại bỏ của nhóm đó bằng (Tổng thời gian của nhóm) trừ đi (Thời gian lớn nhất của nhóm).
**Mã nguồn Java:**
```java
class Solution {
    public int minCost(String colors, int[] neededTime) {
        int totalCost = 0;
        int n = colors.length();
        for (int i = 0; i < n; ) {
            int maxTime = neededTime[i];
            int sumTime = neededTime[i];
            int j = i + 1;
            while (j < n && colors.charAt(j) == colors.charAt(i)) {
                maxTime = Math.max(maxTime, neededTime[j]);
                sumTime += neededTime[j];
                j++;
            }
            if (j > i + 1) {
                totalCost += sumTime - maxTime;
            }
            i = j;
        }
        return totalCost;
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$.

## 14. Smallest String With A Given Numeric Value
**Đề bài chi tiết:** Cho `n` là độ dài chuỗi và `k` là tổng giá trị số. Mỗi ký tự `'a'` tới `'z'` có giá trị 1 tới 26. Trả về chuỗi có độ dài `n`, tổng giá trị `k` và có thứ tự từ điển nhỏ nhất.
**Phân tích thuật toán:**
Để chuỗi có thứ tự từ điển nhỏ nhất, các ký tự ở đầu phải càng nhỏ càng tốt. Ta khởi tạo mảng bằng toàn `'a'` (đã dùng `n` giá trị, số điểm còn lại là `k - n`). Sau đó, dồn các giá trị lớn nhất về cuối chuỗi bằng cách chạy ngược từ cuối lên đầu. Tại mỗi vị trí, cộng thêm tối đa 25 (tức là lên `'z'`) hoặc dư lượng `k` còn lại.
**Mã nguồn Java:**
```java
class Solution {
    public String getSmallestString(int n, int k) {
        char[] res = new char[n];
        Arrays.fill(res, 'a');
        k -= n;
        for (int i = n - 1; i >= 0 && k > 0; i--) {
            int add = Math.min(25, k);
            res[i] = (char) (res[i] + add);
            k -= add;
        }
        return new String(res);
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(N)$.

## 15. Maximum Score From Removing Stones
**Đề bài chi tiết:** Có 3 đống đá `a, b, c`. Mỗi lượt, lấy đi 1 viên đá từ 2 đống khác nhau và nhận 1 điểm. Dừng lại khi chỉ còn nhiều nhất 1 đống đá. Trả về số điểm lớn nhất có thể đạt được.
**Phân tích thuật toán:**
Giả sử $a \le b \le c$. 
- Nếu $a + b \le c$, ta chỉ có thể kết hợp toàn bộ đống $a$ và $b$ với $c$. Số điểm tối đa là $a + b$.
- Nếu $a + b > c$, ta có thể ghép qua lại giữa $a, b, c$ sao cho cuối cùng tổng số đá cả 3 đống chỉ còn tối đa 1 hoặc 0 viên (không bị kẹt đống lớn nhất). Khi đó, ta dùng gần như triệt để số đá, và số điểm tối đa là $(a + b + c) / 2$.
**Mã nguồn Java:**
```java
class Solution {
    public int maximumScore(int a, int b, int c) {
        int[] arr = {a, b, c};
        Arrays.sort(arr);
        if (arr[0] + arr[1] <= arr[2]) {
            return arr[0] + arr[1];
        }
        return (arr[0] + arr[1] + arr[2]) / 2;
    }
}
```
**Độ phức tạp:** Thời gian $O(1)$, Không gian $O(1)$.

## 16. Task Scheduler
**Đề bài chi tiết:** Cho mảng `tasks` và số `n` biểu thị thời gian nghỉ bắt buộc (cooldown) giữa 2 task cùng loại. Trả về khoảng thời gian tối thiểu để hoàn thành tất cả các tasks.
**Phân tích thuật toán:**
Đếm số lần xuất hiện của các task. Task xuất hiện nhiều nhất (giả sử $maxFreq$ lần) sẽ định hình cấu trúc lịch trình với số lượng khoảng trống (intervals). Ta cần sắp xếp task này vào trước, tạo ra các "khoảng trống" (slots). Tổng số slots là $(maxFreq - 1) \times n$. Ta điền các task còn lại vào các khoảng trống. Chiều dài tối thiểu là $(maxFreq - 1) \times (n + 1) + maxCount$. Nếu độ dài này nhỏ hơn `tasks.length`, nghĩa là ta có thể xen kẽ mà không bị trống, đáp án sẽ là `tasks.length`.
**Mã nguồn Java:**
```java
class Solution {
    public int leastInterval(char[] tasks, int n) {
        int[] count = new int[26];
        for (char c : tasks) count[c - 'A']++;
        Arrays.sort(count);
        int maxFreq = count[25];
        int maxCount = 0; // Số lượng task có cùng maxFreq
        for (int i = 25; i >= 0; i--) {
            if (count[i] == maxFreq) maxCount++;
            else break;
        }
        int minLen = (maxFreq - 1) * (n + 1) + maxCount;
        return Math.max(tasks.length, minLen);
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$ (với bảng chữ cái cố định).

## 17. Determine if Two Strings Are Close
**Đề bài chi tiết:** Hai chuỗi `word1` và `word2` được coi là "close" nếu ta có thể chuyển `word1` thành `word2` bằng 2 thao tác: (1) Đổi chỗ 2 ký tự bất kỳ, hoặc (2) Hoán đổi 2 loại ký tự với nhau. Kiểm tra xem chúng có "close" hay không.
**Phân tích thuật toán:**
Thao tác 1 có nghĩa là thứ tự ký tự không quan trọng. Thao tác 2 có nghĩa là các mức tần suất xuất hiện có thể bị hoán đổi. Vậy 2 chuỗi là "close" nếu chúng có cùng độ dài, chứa cùng một tập các ký tự, và có cùng tập hợp các mức tần suất (sau khi sắp xếp tần suất).
**Mã nguồn Java:**
```java
class Solution {
    public boolean closeStrings(String word1, String word2) {
        if (word1.length() != word2.length()) return false;
        int[] freq1 = new int[26];
        int[] freq2 = new int[26];
        for (char c : word1.toCharArray()) freq1[c - 'a']++;
        for (char c : word2.toCharArray()) freq2[c - 'a']++;
        
        for (int i = 0; i < 26; i++) {
            // Tập ký tự phải giống hệt nhau
            if ((freq1[i] == 0 && freq2[i] != 0) || (freq1[i] != 0 && freq2[i] == 0)) {
                return false;
            }
        }
        Arrays.sort(freq1);
        Arrays.sort(freq2);
        for (int i = 0; i < 26; i++) {
            // Tập tần suất phải giống nhau
            if (freq1[i] != freq2[i]) return false;
        }
        return true;
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$.

## 18. Longest Substring Without Repeating Characters
**Đề bài chi tiết:** Cho một chuỗi `s`, tìm chiều dài của chuỗi con dài nhất mà không có bất kỳ ký tự nào bị lặp lại.
**Phân tích thuật toán:**
Sử dụng Sliding Window (Cửa sổ trượt). Sử dụng biến `left` để duy trì điểm bắt đầu của cửa sổ và `right` quét qua mảng. Dùng mảng để lưu vị trí xuất hiện cuối cùng của mỗi ký tự. Khi ký tự ở vị trí `right` bị trùng ở chỉ số lớn hơn hoặc bằng `left`, ta dời `left` sang bên phải của vị trí trùng một bước. Luôn cập nhật chiều dài lớn nhất.
**Mã nguồn Java:**
```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int[] pos = new int[128];
        Arrays.fill(pos, -1);
        int maxLen = 0, left = 0;
        for (int right = 0; right < s.length(); right++) {
            char c = s.charAt(right);
            if (pos[c] >= left) {
                left = pos[c] + 1; // Thu hẹp cửa sổ bằng cách dịch left
            }
            pos[c] = right;
            maxLen = Math.max(maxLen, right - left + 1);
        }
        return maxLen;
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$.

## 19. Sort Characters By Frequency
**Đề bài chi tiết:** Cho chuỗi `s`, sắp xếp chuỗi theo thứ tự tần suất giảm dần của các ký tự. Nếu tần suất bằng nhau, thứ tự tùy ý.
**Phân tích thuật toán:**
Đếm tần suất xuất hiện của các ký tự bằng Hash Map. Sau đó tạo các "xô" (buckets) đại diện cho các mức tần suất từ `1` đến `s.length()`. Nếu một ký tự xuất hiện `f` lần, thêm nó vào bucket thứ `f`. Duyệt ngược từ bucket lớn nhất xuống để xây dựng chuỗi kết quả.
**Mã nguồn Java:**
```java
class Solution {
    public String frequencySort(String s) {
        Map<Character, Integer> count = new HashMap<>();
        for (char c : s.toCharArray()) {
            count.put(c, count.getOrDefault(c, 0) + 1);
        }
        
        List<Character>[] buckets = new List[s.length() + 1];
        for (char c : count.keySet()) {
            int freq = count.get(c);
            if (buckets[freq] == null) buckets[freq] = new ArrayList<>();
            buckets[freq].add(c);
        }
        
        StringBuilder sb = new StringBuilder();
        for (int i = buckets.length - 1; i > 0; i--) {
            if (buckets[i] != null) {
                for (char c : buckets[i]) {
                    for (int j = 0; j < i; j++) sb.append(c);
                }
            }
        }
        return sb.toString();
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(N)$.

## 20. Is Subsequence
**Đề bài chi tiết:** Cho hai chuỗi `s` và `t`. Kiểm tra xem `s` có phải là một chuỗi con (subsequence) của `t` hay không. Chuỗi con không nhất thiết phải liên tiếp.
**Phân tích thuật toán:**
Dùng 2 con trỏ `i` và `j` duyệt qua `s` và `t`. Nếu `s.charAt(i) == t.charAt(j)` thì tăng cả `i` và `j`. Nếu không, chỉ tăng `j`. Thuật toán tham lam này hoạt động vì ta luôn muốn ghép ký tự đầu tiên gặp được, việc đợi một ký tự y hệt ở phía sau không bao giờ tốt hơn. Nếu `i` đi hết chuỗi `s` thì là chuỗi con.
**Mã nguồn Java:**
```java
class Solution {
    public boolean isSubsequence(String s, String t) {
        int i = 0, j = 0;
        while (i < s.length() && j < t.length()) {
            if (s.charAt(i) == t.charAt(j)) {
                i++;
            }
            j++;
        }
        return i == s.length();
    }
}
```
**Độ phức tạp:** Thời gian $O(M + N)$ (với $M, N$ là chiều dài của `s` và `t`), Không gian $O(1)$.

## 21. Append Characters to String to Make Subsequence
**Đề bài chi tiết:** Cho 2 chuỗi `s` và `t`. Tính số ký tự ít nhất cần thêm vào cuối chuỗi `s` để chuỗi `t` trở thành một chuỗi con (subsequence) của `s`.
**Phân tích thuật toán:**
Dùng 2 con trỏ `i` và `j` để duyệt chuỗi `s` và `t`. Tương tự bài toán "Is Subsequence", nếu `s.charAt(i) == t.charAt(j)` thì ta tăng cả `i` và `j`. Nếu không, chỉ tăng `i`. Sau khi duyệt hết chuỗi `s`, phần còn lại của `t` từ vị trí `j` cho đến cuối là những ký tự chưa khớp. Vì vậy ta cần thêm đúng các ký tự này vào cuối `s`. Số ký tự cần thêm là `t.length() - j`.
**Mã nguồn Java:**
```java
class Solution {
    public int appendCharacters(String s, String t) {
        int i = 0, j = 0;
        int n = s.length(), m = t.length();
        while (i < n && j < m) {
            if (s.charAt(i) == t.charAt(j)) {
                j++;
            }
            i++;
        }
        return m - j;
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$ với $N$ là độ dài của `s`, Không gian $O(1)$.

## 22. Rearrange Characters to Make Target String
**Đề bài chi tiết:** Cho chuỗi `s` (chứa các ký tự có sẵn) và chuỗi `target` (chuỗi mục tiêu). Bạn có thể tạo được tối đa bao nhiêu bản sao của chuỗi `target` từ các ký tự của `s`?
**Phân tích thuật toán:**
Đếm số lần xuất hiện của mỗi ký tự trong cả `s` và `target`. Duyệt qua các ký tự của `target`, số bản sao tối đa của `target` có thể tạo ra sẽ bị giới hạn bởi ký tự có tỷ lệ `count_s / count_target` nhỏ nhất.
**Mã nguồn Java:**
```java
class Solution {
    public int rearrangeCharacters(String s, String target) {
        int[] freqS = new int[26];
        int[] freqTarget = new int[26];
        for (char c : s.toCharArray()) freqS[c - 'a']++;
        for (char c : target.toCharArray()) freqTarget[c - 'a']++;
        
        int maxCopies = Integer.MAX_VALUE;
        for (int i = 0; i < 26; i++) {
            if (freqTarget[i] > 0) {
                maxCopies = Math.min(maxCopies, freqS[i] / freqTarget[i]);
            }
        }
        return maxCopies;
    }
}
```
**Độ phức tạp:** Thời gian $O(N + M)$ (với $N$ và $M$ là chiều dài của `s` và `target`), Không gian $O(1)$.

## 23. Minimum Amount of Time to Collect Garbage
**Đề bài chi tiết:** Cho mảng `garbage` đại diện cho rác tại mỗi nhà (chứa các ký tự 'M', 'P', 'G' là loại rác) và mảng `travel` đại diện cho thời gian di chuyển giữa các nhà. Có 3 xe rác chuyên biệt cho từng loại. Mỗi xe mất 1 phút để nhặt 1 túi rác. Tính tổng thời gian tối thiểu để nhặt toàn bộ rác.
**Phân tích thuật toán:**
Thời gian nhặt rác hoàn toàn cố định: Mỗi túi rác mất 1 phút $\rightarrow$ bằng tổng số lượng túi rác trong mảng.
Thời gian di chuyển: Xe chỉ cần đi đến ngôi nhà cuối cùng có loại rác tương ứng. Do đó ta tìm vị trí cuối cùng chứa 'M', 'P' và 'G'. Sau đó cộng dồn khoảng cách `travel` từ nhà 0 đến vị trí cuối cùng của từng loại bằng mảng cộng dồn (prefix sum).
**Mã nguồn Java:**
```java
class Solution {
    public int garbageCollection(String[] garbage, int[] travel) {
        int[] lastPos = new int[128];
        int totalGarbage = 0;
        
        for (int i = 0; i < garbage.length; i++) {
            totalGarbage += garbage[i].length();
            for (char c : garbage[i].toCharArray()) {
                lastPos[c] = i;
            }
        }
        
        int[] prefixTravel = new int[travel.length + 1];
        for (int i = 0; i < travel.length; i++) {
            prefixTravel[i + 1] = prefixTravel[i] + travel[i];
        }
        
        int totalTime = totalGarbage;
        char[] types = {'M', 'P', 'G'};
        for (char c : types) {
            if (lastPos[c] > 0) {
                totalTime += prefixTravel[lastPos[c]];
            }
        }
        return totalTime;
    }
}
```
**Độ phức tạp:** Thời gian $O(K)$ (với $K$ là tổng chiều dài tất cả các chuỗi trong mảng `garbage`), Không gian $O(N)$ cho `prefixTravel` hoặc có thể tối ưu thành $O(1)$.

## 24. Number of Matching Subsequences
**Đề bài chi tiết:** Cho chuỗi `s` và một mảng các chuỗi `words`. Trả về số lượng `words[i]` là chuỗi con của `s`.
**Phân tích thuật toán:**
Nếu kiểm tra từng chuỗi con bằng `isSubsequence` thì sẽ bị quá thời gian. Thay vì tìm `s` trong mỗi `word`, ta duyệt chuỗi `s` đúng 1 lần và cập nhật đồng thời nhiều `word`. Sử dụng 26 "xô" (buckets) tương ứng với 26 chữ cái. Ban đầu đặt các `word` vào xô dựa trên ký tự đầu tiên của nó. Duyệt qua từng ký tự `c` trong `s`, lấy các `word` trong xô `c`, bỏ ký tự đầu tiên đi và đưa phần còn lại vào xô tương ứng với ký tự tiếp theo. Nếu `word` đã hết ký tự, ta tăng biến đếm kết quả.
**Mã nguồn Java:**
```java
class Solution {
    public int numMatchingSubseq(String s, String[] words) {
        List<Integer>[] buckets = new ArrayList[26];
        for (int i = 0; i < 26; i++) buckets[i] = new ArrayList<>();
        
        int[] wordPtr = new int[words.length];
        for (int i = 0; i < words.length; i++) {
            buckets[words[i].charAt(0) - 'a'].add(i);
        }
        
        int res = 0;
        for (char c : s.toCharArray()) {
            List<Integer> currBucket = buckets[c - 'a'];
            buckets[c - 'a'] = new ArrayList<>(); // reset xô
            
            for (int idx : currBucket) {
                wordPtr[idx]++;
                if (wordPtr[idx] == words[idx].length()) {
                    res++;
                } else {
                    char nextChar = words[idx].charAt(wordPtr[idx]);
                    buckets[nextChar - 'a'].add(idx);
                }
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Thời gian $O(S + \sum W)$ với $S$ là độ dài của `s` và $\sum W$ là tổng độ dài của mảng `words`. Không gian $O(K)$ số lượng `words`.

## 25. Maximum Number of Occurrences of a Substring
**Đề bài chi tiết:** Cho chuỗi `s` và các số `maxLetters` (số lượng ký tự phân biệt tối đa), `minSize` và `maxSize` của chuỗi con. Tìm số lần xuất hiện nhiều nhất của một chuỗi con hợp lệ.
**Phân tích thuật toán:**
Do chuỗi con xuất hiện nhiều nhất có chiều dài `L` (với `minSize <= L <= maxSize`) luôn chứa chuỗi con của nó có chiều dài `minSize`. Nên bất kỳ chuỗi độ dài `L` nào lặp lại thì chuỗi `minSize` bên trong nó cũng lặp lại số lần tương đương. Vì vậy, theo Greedy, ta chỉ cần kiểm tra các chuỗi con có độ dài chính xác bằng `minSize`. Dùng mảng cờ để đếm số ký tự phân biệt và HashMap để đếm số lần xuất hiện.
**Mã nguồn Java:**
```java
class Solution {
    public int maxFreq(String s, int maxLetters, int minSize, int maxSize) {
        Map<String, Integer> countMap = new HashMap<>();
        int maxOccurrences = 0;
        
        for (int i = 0; i <= s.length() - minSize; i++) {
            String sub = s.substring(i, i + minSize);
            // Đếm số ký tự duy nhất
            int unique = 0;
            boolean[] seen = new boolean[26];
            for (char c : sub.toCharArray()) {
                if (!seen[c - 'a']) {
                    seen[c - 'a'] = true;
                    unique++;
                }
            }
            if (unique <= maxLetters) {
                int freq = countMap.getOrDefault(sub, 0) + 1;
                countMap.put(sub, freq);
                maxOccurrences = Math.max(maxOccurrences, freq);
            }
        }
        return maxOccurrences;
    }
}
```
**Độ phức tạp:** Thời gian $O(N \times minSize)$, Không gian $O(N \times minSize)$. (Có thể tối ưu bằng Sliding Window hoàn chỉnh hơn).

## 26. Split a String in Balanced Strings
**Đề bài chi tiết:** Cho chuỗi `s` chỉ chứa `'L'` và `'R'`. Chuỗi cân bằng là chuỗi có số `'L'` bằng số `'R'`. Chia `s` thành nhiều chuỗi cân bằng nhất có thể và trả về số lượng đó.
**Phân tích thuật toán:**
Duyệt chuỗi từ trái sang phải, dùng biến `balance` (cộng 1 nếu `'R'`, trừ 1 nếu `'L'`). Bất cứ khi nào `balance == 0`, ta đã tìm thấy một chuỗi cân bằng, nên ta tham lam tăng kết quả lên 1 và tiếp tục (việc cắt ngay lập tức sẽ luôn cho số chuỗi nhiều nhất).
**Mã nguồn Java:**
```java
class Solution {
    public int balancedStringSplit(String s) {
        int balance = 0, count = 0;
        for (char c : s.toCharArray()) {
            if (c == 'R') balance++;
            else balance--;
            
            if (balance == 0) {
                count++;
            }
        }
        return count;
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$.

## 27. Remove All Adjacent Duplicates in String
**Đề bài chi tiết:** Cho chuỗi `s`. Một lần xóa kề nhau là chọn 2 ký tự giống nhau liên tiếp và xóa chúng. Thực hiện cho đến khi không thể xóa được nữa.
**Phân tích thuật toán:**
Dùng Stack (hoặc StringBuilder). Đưa từng ký tự vào. Nếu ký tự hiện tại giống với đỉnh Stack, ta loại bỏ đỉnh Stack (tham lam xóa ngay lập tức). Ngược lại, đẩy ký tự đó vào Stack.
**Mã nguồn Java:**
```java
class Solution {
    public String removeDuplicates(String s) {
        StringBuilder sb = new StringBuilder();
        for (char c : s.toCharArray()) {
            int len = sb.length();
            if (len > 0 && sb.charAt(len - 1) == c) {
                sb.deleteCharAt(len - 1);
            } else {
                sb.append(c);
            }
        }
        return sb.toString();
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(N)$.

## 28. String Without AAA or BBB
**Đề bài chi tiết:** Cho 2 số `a` và `b`. Trả về chuỗi độ dài `a + b` chứa chính xác `a` ký tự `'a'` và `b` ký tự `'b'`, sao cho không có chuỗi con `"aaa"` hoặc `"bbb"`.
**Phân tích thuật toán:**
Xây dựng chuỗi từng ký tự một bằng tham lam. Tại mỗi bước, nếu 2 ký tự cuối cùng đã giống nhau (ví dụ "aa"), ta bắt buộc phải điền ký tự còn lại ('b'). Nếu không, ta luôn ưu tiên điền ký tự đang có số lượng còn lại nhiều hơn để tránh việc bị dư quá nhiều một loại ký tự ở cuối.
**Mã nguồn Java:**
```java
class Solution {
    public String strWithout3a3b(int a, int b) {
        StringBuilder sb = new StringBuilder();
        while (a > 0 || b > 0) {
            boolean writeA = false;
            int len = sb.length();
            if (len >= 2 && sb.charAt(len - 1) == sb.charAt(len - 2)) {
                // Buộc phải đổi ký tự
                if (sb.charAt(len - 1) == 'b') writeA = true;
            } else {
                // Chọn ký tự còn nhiều hơn
                if (a >= b) writeA = true;
            }
            
            if (writeA) {
                sb.append('a');
                a--;
            } else {
                sb.append('b');
                b--;
            }
        }
        return sb.toString();
    }
}
```
**Độ phức tạp:** Thời gian $O(A + B)$, Không gian $O(A + B)$.

## 29. Check If Word Is Valid After Substitutions
**Đề bài chi tiết:** Một chuỗi hợp lệ nếu nó rỗng hoặc được tạo bằng cách chèn `"abc"` vào bất kỳ vị trí nào của một chuỗi hợp lệ. Kiểm tra chuỗi `s` có hợp lệ không.
**Phân tích thuật toán:**
Dùng Stack (hoặc mảng ký tự). Đọc từng ký tự. Khi gặp ký tự `'c'`, ta kiểm tra 2 ký tự đỉnh Stack có phải là `'b'` và `'a'` hay không. Nếu đúng, ta tham lam "xóa" `"abc"` bằng cách pop cả 2 ra khỏi Stack. Nếu cuối cùng Stack rỗng thì hợp lệ.
**Mã nguồn Java:**
```java
class Solution {
    public boolean isValid(String s) {
        char[] stack = new char[s.length()];
        int top = -1;
        for (char c : s.toCharArray()) {
            if (c == 'c') {
                if (top >= 1 && stack[top] == 'b' && stack[top - 1] == 'a') {
                    top -= 2;
                } else {
                    return false;
                }
            } else {
                stack[++top] = c;
            }
        }
        return top == -1;
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(N)$.

## 30. Score of Parentheses
**Đề bài chi tiết:** Cho chuỗi `s` là một dãy ngoặc hợp lệ. Cặp `()` điểm bằng 1. `AB` có điểm là `A + B`. `(A)` có điểm là `2 * A`. Trả về tổng điểm.
**Phân tích thuật toán:**
Thay vì tính toán theo từng phần từ dưới lên, ta nhận thấy chỉ có phần lõi `()` mới thực sự đóng góp điểm (1 điểm). Mỗi phần lõi này nếu nằm trong `d` cặp ngoặc bao bên ngoài thì sẽ được nhân lên $2^d$ lần (hay `1 << d`). Ta duyệt chuỗi, theo dõi độ sâu `depth`. Mỗi lần gặp `(`, `depth++`. Khi gặp `)`, `depth--`. Nếu gặp trực tiếp cặp `()` (tức là `s.charAt(i-1) == '('`), ta cộng `1 << depth` vào tổng điểm.
**Mã nguồn Java:**
```java
class Solution {
    public int scoreOfParentheses(String s) {
        int score = 0, depth = 0;
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '(') {
                depth++;
            } else {
                depth--;
                if (s.charAt(i - 1) == '(') {
                    score += (1 << depth);
                }
            }
        }
        return score;
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$.
