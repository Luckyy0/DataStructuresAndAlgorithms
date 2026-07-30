# 05 - Advanced Backtracking Problems (Bài tập Quay lui Nâng cao)

## 1. N-Queens (Bitmask optimized approach)
**Đề bài chi tiết:**
Đặt N quân hậu lên bàn cờ N x N sao cho không có 2 quân hậu nào tấn công lẫn nhau. Trả về tất cả các cách sắp xếp có thể.
*(Yêu cầu sử dụng kỹ thuật Bitmask để tối ưu trạng thái thay vì mảng boolean hoặc HashSet).*

**Phân tích thuật toán:**
Dùng 3 số nguyên (integer bitmask) để lưu vết:
- `cols`: Đánh dấu các cột đã có quân hậu.
- `diag1`: Đánh dấu các đường chéo chính `(\)`.
- `diag2`: Đánh dấu các đường chéo phụ `(/)`.
Với mỗi dòng, tính các vị trí khả dụng: `availablePositions = ~(cols | diag1 | diag2) & ((1 << n) - 1)`.
Duyệt qua các bit 1 trong `availablePositions` để đặt hậu, sau đó đệ quy xuống dòng tiếp theo.

**Mã nguồn Java:**
```java
import java.util.*;

public class NQueensBitmask {
    List<List<String>> result = new ArrayList<>();
    
    public List<List<String>> solveNQueens(int n) {
        int[] board = new int[n];
        solve(0, 0, 0, 0, n, board);
        return result;
    }
    
    private void solve(int row, int cols, int diag1, int diag2, int n, int[] board) {
        if (row == n) {
            result.add(generateBoard(board, n));
            return;
        }
        
        int available = ((1 << n) - 1) & ~(cols | diag1 | diag2);
        while (available > 0) {
            int p = available & -available; // Lấy bit 1 thấp nhất
            available ^= p; // Xóa bit p
            
            int colIndex = Integer.numberOfTrailingZeros(p);
            board[row] = colIndex;
            
            solve(row + 1, cols | p, (diag1 | p) << 1, (diag2 | p) >> 1, n, board);
        }
    }
    
    private List<String> generateBoard(int[] board, int n) {
        List<String> res = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            char[] row = new char[n];
            Arrays.fill(row, '.');
            row[board[i]] = 'Q';
            res.add(new String(row));
        }
        return res;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): O(N!)
- Không gian (Space Complexity): O(N) đệ quy stack.

---

## 2. Remove Invalid Parentheses
**Đề bài chi tiết:**
Cho một chuỗi gồm các ký tự chữ cái, `(` và `)`. Xóa số lượng ngoặc tối thiểu để thu được một chuỗi hợp lệ. Trả về tất cả các kết quả hợp lệ có thể có.

**Phân tích thuật toán:**
Đầu tiên, đếm số ngoặc `(` và `)` dư thừa cần xóa (`leftRemove`, `rightRemove`).
Sử dụng Backtracking kết hợp với chỉ số `index` duyệt qua chuỗi:
- Nếu xóa ký tự hiện tại, giảm `leftRemove` hoặc `rightRemove`, đệ quy đi tiếp.
- Nếu giữ ký tự hiện tại, cập nhật độ cân bằng (balance), nếu balance < 0 (quá nhiều `)`) thì cắt tỉa (prune).
Lưu ý dùng `HashSet` để tránh kết quả trùng lặp.

**Mã nguồn Java:**
```java
import java.util.*;

public class RemoveInvalidParentheses {
    public List<String> removeInvalidParentheses(String s) {
        int leftRemove = 0, rightRemove = 0;
        for (char c : s.toCharArray()) {
            if (c == '(') leftRemove++;
            else if (c == ')') {
                if (leftRemove > 0) leftRemove--;
                else rightRemove++;
            }
        }
        
        Set<String> validExpressions = new HashSet<>();
        backtrack(s, 0, leftRemove, rightRemove, 0, new StringBuilder(), validExpressions);
        return new ArrayList<>(validExpressions);
    }
    
    private void backtrack(String s, int index, int leftRemove, int rightRemove, int balance, StringBuilder path, Set<String> res) {
        if (index == s.length()) {
            if (leftRemove == 0 && rightRemove == 0 && balance == 0) {
                res.add(path.toString());
            }
            return;
        }
        if (balance < 0) return;
        
        char c = s.charAt(index);
        int len = path.length();
        
        // Nhánh 1: Bỏ qua (xóa) ký tự hiện tại nếu nó là ngoặc
        if (c == '(' && leftRemove > 0) {
            backtrack(s, index + 1, leftRemove - 1, rightRemove, balance, path, res);
        }
        if (c == ')' && rightRemove > 0) {
            backtrack(s, index + 1, leftRemove, rightRemove - 1, balance, path, res);
        }
        
        // Nhánh 2: Giữ lại ký tự
        path.append(c);
        if (c == '(') backtrack(s, index + 1, leftRemove, rightRemove, balance + 1, path, res);
        else if (c == ')') backtrack(s, index + 1, leftRemove, rightRemove, balance - 1, path, res);
        else backtrack(s, index + 1, leftRemove, rightRemove, balance, path, res); // Chữ cái
        
        // Backtrack
        path.setLength(len);
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): O(2^N)
- Không gian (Space Complexity): O(N) stack call.

---

## 3. Regular Expression Matching (Backtracking view)
**Đề bài chi tiết:**
Cho chuỗi đầu vào `s` và một mẫu (pattern) `p`, thực hiện so khớp regex có hỗ trợ `.` (khớp một ký tự) và `*` (khớp 0 hoặc nhiều ký tự trước nó). (Triển khai bằng Backtracking thay vì DP để hiểu rõ trạng thái).

**Phân tích thuật toán:**
Dùng hàm `match(i, j)` để kiểm tra khớp `s[i:]` và `p[j:]`.
Nếu ký tự tiếp theo trong `p` là `*` (tại `j+1`):
- Nhánh 1: Coi `*` khớp với 0 ký tự (`match(i, j+2)`).
- Nhánh 2: Nếu `s[i]` khớp với `p[j]`, coi `*` khớp với 1 hoặc nhiều ký tự (`match(i+1, j)`).
Có thể memoize (nhớ kết quả) để giảm thời gian.

**Mã nguồn Java:**
```java
public class RegexMatchingBacktrack {
    public boolean isMatch(String s, String p) {
        return backtrack(s, p, 0, 0);
    }
    
    private boolean backtrack(String s, String p, int i, int j) {
        if (j == p.length()) return i == s.length();
        
        boolean firstMatch = (i < s.length() && 
                             (s.charAt(i) == p.charAt(j) || p.charAt(j) == '.'));
                             
        if (j + 1 < p.length() && p.charAt(j + 1) == '*') {
            // '*' match 0 lần HOẶC khớp >=1 lần (nếu firstMatch)
            return backtrack(s, p, i, j + 2) || (firstMatch && backtrack(s, p, i + 1, j));
        } else {
            return firstMatch && backtrack(s, p, i + 1, j + 1);
        }
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): O((S+P)*2^(S+P/2)) trong trường hợp xấu nhất (nếu không dùng memoization).
- Không gian (Space Complexity): O(S + P) do call stack.

---

## 4. Wildcard Matching (Backtracking view)
**Đề bài chi tiết:**
Cho chuỗi `s` và pattern `p` chứa `?` (khớp 1 ký tự) và `*` (khớp chuỗi ký tự bất kỳ, độ dài >= 0). Triển khai bằng Backtracking.

**Phân tích thuật toán:**
Nếu gặp `*`, ta có thể thử mọi vị trí khớp có thể trên `s`. Để tránh TLE (Time Limit Exceeded) khi dùng Backtracking thuần, ta áp dụng kỹ thuật Greedy + Backtracking: chỉ lưu vết (backtrack point) cuối cùng của dấu `*` và quay lại đó nếu thất bại, không cần lưu toàn bộ call stack.

**Mã nguồn Java:**
```java
public class WildcardMatchingBacktrack {
    public boolean isMatch(String s, String p) {
        int i = 0, j = 0;
        int matchIdx = -1, starIdx = -1;
        
        while (i < s.length()) {
            if (j < p.length() && (p.charAt(j) == '?' || p.charAt(j) == s.charAt(i))) {
                i++; j++;
            } else if (j < p.length() && p.charAt(j) == '*') {
                starIdx = j;
                matchIdx = i;
                j++;
            } else if (starIdx != -1) {
                // Backtrack: Quay lại dấu * cuối cùng, thử khớp thêm 1 ký tự ở s
                j = starIdx + 1;
                matchIdx++;
                i = matchIdx;
            } else {
                return false;
            }
        }
        
        while (j < p.length() && p.charAt(j) == '*') {
            j++;
        }
        
        return j == p.length();
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): O(S * P) worst case, O(S + P) average case.
- Không gian (Space Complexity): O(1).

---

## 5. Find Minimum Time to Finish All Jobs
**Đề bài chi tiết:**
Có `k` công nhân và mảng `jobs` chứa thời gian hoàn thành từng công việc. Cần phân bổ tất cả công việc cho các công nhân sao cho thời gian làm việc tối đa của một công nhân là nhỏ nhất.

**Phân tích thuật toán:**
Dùng Backtracking kết hợp Branch and Bound. Sắp xếp mảng `jobs` giảm dần để ưu tiên gán việc lớn trước (Heuristic).
Khởi tạo mảng `workload[]` cho `k` công nhân. Duyệt qua từng `job`, thử gán cho từng công nhân, cập nhật workload và đệ quy. 
Pruning: 
- `if (workload[i] + jobs[jobIdx] >= ans) continue;`
- Nếu gán cho công nhân có workload == 0 và bị fail thì không cần thử tiếp các công nhân khác có workload = 0.

**Mã nguồn Java:**
```java
import java.util.*;

public class MinimumTimeJobs {
    int ans = Integer.MAX_VALUE;
    
    public int minimumTimeRequired(int[] jobs, int k) {
        // Sort descending to assign big jobs first
        Arrays.sort(jobs);
        for(int i = 0, j = jobs.length - 1; i < j; i++, j--) {
            int temp = jobs[i]; jobs[i] = jobs[j]; jobs[j] = temp;
        }
        
        int[] workload = new int[k];
        backtrack(jobs, workload, 0, 0);
        return ans;
    }
    
    private void backtrack(int[] jobs, int[] workload, int jobIdx, int currentMax) {
        if (currentMax >= ans) return; // Pruning
        if (jobIdx == jobs.length) {
            ans = Math.min(ans, currentMax);
            return;
        }
        
        for (int i = 0; i < workload.length; i++) {
            if (workload[i] + jobs[jobIdx] >= ans) continue;
            
            workload[i] += jobs[jobIdx];
            backtrack(jobs, workload, jobIdx + 1, Math.max(currentMax, workload[i]));
            workload[i] -= jobs[jobIdx]; // Backtrack
            
            // Symmetry breaking / Pruning
            if (workload[i] == 0) break; 
        }
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): O(K^N), được giảm thiểu nhiều nhờ Pruning.
- Không gian (Space Complexity): O(N + K).

---

## 6. Maximum Score Words Formed by Letters
**Đề bài chi tiết:**
Cho mảng từ `words`, mảng các ký tự `letters` (có thể lặp lại), và mảng `score` của 26 chữ cái. Tính điểm tối đa có thể đạt được bằng cách ghép các từ hợp lệ (mỗi ký tự trong letters chỉ được dùng 1 lần).

**Phân tích thuật toán:**
Sử dụng mảng đếm tần suất của `letters`.
Duyệt qua từng `word` trong `words` bằng Backtracking:
- Nhánh 1: Bỏ qua `word` hiện tại.
- Nhánh 2: Nếu đủ ký tự, chọn `word` hiện tại, trừ tần suất ký tự, tính điểm, đệ quy tiếp, sau đó phục hồi trạng thái (backtrack).

**Mã nguồn Java:**
```java
public class MaxScoreWords {
    public int maxScoreWords(String[] words, char[] letters, int[] score) {
        int[] count = new int[26];
        for (char c : letters) count[c - 'a']++;
        return backtrack(words, count, score, 0);
    }
    
    private int backtrack(String[] words, int[] count, int[] score, int idx) {
        if (idx == words.length) return 0;
        
        // Bỏ qua từ hiện tại
        int maxScore = backtrack(words, count, score, idx + 1);
        
        // Thử chọn từ hiện tại
        int wordScore = 0;
        boolean isValid = true;
        int[] tempCount = count.clone();
        
        for (char c : words[idx].toCharArray()) {
            tempCount[c - 'a']--;
            if (tempCount[c - 'a'] < 0) isValid = false;
            wordScore += score[c - 'a'];
        }
        
        if (isValid) {
            maxScore = Math.max(maxScore, wordScore + backtrack(words, tempCount, score, idx + 1));
        }
        
        return maxScore;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): O(2^N * L), N số từ, L độ dài trung bình.
- Không gian (Space Complexity): O(N * 26) = O(N) mảng tempCount.

---

## 7. Verbal Arithmetic Puzzle
**Đề bài chi tiết:**
Giải bài toán mã hóa chữ cái thành số (VD: `SEND + MORE = MONEY`). Cho mảng `words` và `result`. Gán mỗi chữ cái một chữ số từ 0-9 duy nhất sao cho tổng hợp lệ. Ký tự đầu tiên không được bằng 0. Trả về `true` nếu giải được.

**Phân tích thuật toán:**
Dùng Backtracking duyệt từng chữ số từ phải sang trái (hàng đơn vị lên).
Vì duyệt từ phải qua, duy trì biến `carry` (nhớ).
Dùng HashMap hoặc mảng độ dài 26 để ánh xạ chữ cái -> số, và boolean array `used[10]` để đánh dấu số đã dùng.

**Mã nguồn Java:**
```java
import java.util.*;

public class VerbalArithmetic {
    int[] charToDigit = new int[26];
    boolean[] used = new boolean[10];
    
    public boolean isSolvable(String[] words, String result) {
        Arrays.fill(charToDigit, -1);
        int maxLen = result.length();
        for (String w : words) if (w.length() > maxLen) return false;
        
        return backtrack(words, result, 0, 0, 0);
    }
    
    private boolean backtrack(String[] words, String result, int digitIdx, int wordIdx, int carry) {
        if (digitIdx == result.length()) return carry == 0;
        
        if (wordIdx == words.length) {
            int sum = carry;
            char targetChar = result.charAt(result.length() - 1 - digitIdx);
            int expectedDigit = sum % 10;
            
            if (charToDigit[targetChar - 'A'] == -1) {
                if (used[expectedDigit] || (expectedDigit == 0 && result.length() > 1 && digitIdx == result.length() - 1)) return false;
                charToDigit[targetChar - 'A'] = expectedDigit;
                used[expectedDigit] = true;
                boolean res = backtrack(words, result, digitIdx + 1, 0, sum / 10);
                charToDigit[targetChar - 'A'] = -1;
                used[expectedDigit] = false;
                return res;
            } else {
                if (charToDigit[targetChar - 'A'] == expectedDigit) {
                    return backtrack(words, result, digitIdx + 1, 0, sum / 10);
                }
                return false;
            }
        }
        
        String word = words[wordIdx];
        if (digitIdx >= word.length()) {
            return backtrack(words, result, digitIdx, wordIdx + 1, carry);
        }
        
        char c = word.charAt(word.length() - 1 - digitIdx);
        if (charToDigit[c - 'A'] != -1) {
            return backtrack(words, result, digitIdx, wordIdx + 1, carry + charToDigit[c - 'A']);
        } else {
            for (int d = 0; d <= 9; d++) {
                if (used[d] || (d == 0 && word.length() > 1 && digitIdx == word.length() - 1)) continue;
                charToDigit[c - 'A'] = d;
                used[d] = true;
                if (backtrack(words, result, digitIdx, wordIdx + 1, carry + d)) return true;
                used[d] = false;
                charToDigit[c - 'A'] = -1;
            }
            return false;
        }
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): Không gian trạng thái rất lớn (10!), nhưng nhờ Branch & Bound sớm (carry, match), thời gian giảm nhiều.
- Không gian (Space Complexity): O(maxLen).

---

## 8. 24 Game
**Đề bài chi tiết:**
Cho 4 số, kiểm tra xem có thể dùng các phép toán `+`, `-`, `*`, `/` và dấu ngoặc `()` để tạo ra kết quả = 24 hay không. 

**Phân tích thuật toán:**
Mỗi bước, chọn 2 số trong mảng, thực hiện 1 trong 4 phép toán, tạo ra 1 số mới, bỏ 2 số cũ và đưa số mới vào mảng (Kích thước mảng giảm 1).
Khi mảng còn 1 phần tử, kiểm tra nếu sai số so với 24 < 1e-6 (để xử lý số thực `/`).
Dùng Backtracking.

**Mã nguồn Java:**
```java
import java.util.*;

public class Game24 {
    public boolean judgePoint24(int[] cards) {
        List<Double> list = new ArrayList<>();
        for (int v : cards) list.add((double) v);
        return backtrack(list);
    }
    
    private boolean backtrack(List<Double> list) {
        if (list.size() == 1) {
            return Math.abs(list.get(0) - 24.0) < 1e-6;
        }
        
        for (int i = 0; i < list.size(); i++) {
            for (int j = 0; j < list.size(); j++) {
                if (i != j) {
                    List<Double> next = new ArrayList<>();
                    for (int k = 0; k < list.size(); k++) {
                        if (k != i && k != j) next.add(list.get(k));
                    }
                    
                    double[] ops = new double[]{
                        list.get(i) + list.get(j),
                        list.get(i) - list.get(j),
                        list.get(j) - list.get(i),
                        list.get(i) * list.get(j),
                        list.get(i) / list.get(j),
                        list.get(j) / list.get(i)
                    };
                    
                    for (double val : ops) {
                        next.add(val);
                        if (backtrack(next)) return true;
                        next.remove(next.size() - 1);
                    }
                }
            }
        }
        return false;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): O(4! * 4^3) ~ O(1) do số lượng số cố định = 4.
- Không gian (Space Complexity): O(1) (Stack depth 4).

---

## 9. Number of Squareful Arrays
**Đề bài chi tiết:**
Cho mảng `A`, tính số lượng các hoán vị của `A` sao cho mọi phần tử kề nhau đều có tổng là một số chính phương. Các phần tử trùng lặp được xem là hoán vị riêng nếu đổi chỗ, nhưng kết quả trả về là số hoán vị unique.

**Phân tích thuật toán:**
Giống bài Permutation II, sort mảng ban đầu để giải quyết trùng lặp.
Khi xây dựng mảng hoán vị (Backtracking), kiểm tra phần tử chuẩn bị thêm có tạo thành số chính phương với phần tử cuối hiện tại không.
Nếu không, prune sớm.

**Mã nguồn Java:**
```java
import java.util.*;

public class SquarefulArrays {
    int count = 0;
    
    public int numSquarefulPerms(int[] nums) {
        Arrays.sort(nums);
        boolean[] used = new boolean[nums.length];
        backtrack(nums, used, new ArrayList<>());
        return count;
    }
    
    private void backtrack(int[] nums, boolean[] used, List<Integer> path) {
        if (path.size() == nums.length) {
            count++;
            return;
        }
        
        for (int i = 0; i < nums.length; i++) {
            if (used[i]) continue;
            // Bỏ qua trùng lặp
            if (i > 0 && nums[i] == nums[i-1] && !used[i-1]) continue;
            
            if (path.isEmpty() || isSquare(path.get(path.size() - 1) + nums[i])) {
                used[i] = true;
                path.add(nums[i]);
                backtrack(nums, used, path);
                path.remove(path.size() - 1);
                used[i] = false;
            }
        }
    }
    
    private boolean isSquare(int val) {
        int root = (int) Math.sqrt(val);
        return root * root == val;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): O(N!) worst case, nhưng nhờ pruning sẽ thấp hơn rất nhiều.
- Không gian (Space Complexity): O(N).

---

## 10. Robot Room Cleaner
**Đề bài chi tiết:**
Cho một robot dọn dẹp phòng, không có bản đồ phòng. Cung cấp API `move()`, `turnLeft()`, `turnRight()`, `clean()`. Thuật toán để robot dọn dẹp toàn bộ các ô hợp lệ.

**Phân tích thuật toán:**
Dùng DFS/Backtracking. Robot tự định vị trí tương đối `(0, 0)` tại điểm xuất phát, đánh dấu các ô đã đi qua bằng `Set<String>`. 
Sử dụng mảng `directions` (Lên, Phải, Xuống, Trái).
Mỗi lần đệ quy, thử đi tới 4 hướng, gọi API `move()`. 
**Chú ý Backtrack**: Sau khi thăm xong 1 nhánh, robot phải quay về ô cũ và quay lại hướng ban đầu (turnRight 2 lần, move, turnRight 2 lần).

**Mã nguồn Java:**
```java
import java.util.*;

// Interface giả lập
interface Robot {
    boolean move();
    void turnLeft();
    void turnRight();
    void clean();
}

public class RobotCleaner {
    // 0: Lên, 1: Phải, 2: Xuống, 3: Trái
    int[][] dirs = {{-1, 0}, {0, 1}, {1, 0}, {0, -1}};
    Set<String> visited = new HashSet<>();
    
    public void cleanRoom(Robot robot) {
        backtrack(robot, 0, 0, 0);
    }
    
    private void backtrack(Robot robot, int x, int y, int dir) {
        visited.add(x + "," + y);
        robot.clean();
        
        for (int i = 0; i < 4; i++) {
            int newDir = (dir + i) % 4;
            int nx = x + dirs[newDir][0];
            int ny = y + dirs[newDir][1];
            
            if (!visited.contains(nx + "," + ny) && robot.move()) {
                backtrack(robot, nx, ny, newDir);
                // Backtrack: Quay lưng đi lùi 1 bước, hướng cũ giữ nguyên
                robot.turnRight();
                robot.turnRight();
                robot.move();
                robot.turnRight();
                robot.turnRight();
            }
            // Quay mặt sang phải để duyệt hướng tiếp theo
            robot.turnRight();
        }
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): O(E) = O(Số lượng ô trống), robot thăm mỗi ô 1 lần.
- Không gian (Space Complexity): O(Số lượng ô trống) cho Set lưu vết.

---

## 11. Word Squares
**Đề bài chi tiết:**
Cho một mảng các từ, tìm tất cả các "hình vuông từ vựng" (word squares). Một hình vuông từ vựng là một ma trận chữ cái $N \times N$ sao cho việc đọc theo hàng ngang thứ $k$ và cột dọc thứ $k$ tạo ra cùng một từ. 

**Phân tích thuật toán:**
Sử dụng Backtracking để xây dựng từng hàng. Để tăng tốc độ tìm kiếm các từ bắt đầu bằng một tiền tố (prefix) cụ thể (được hình thành từ các ký tự đã chọn ở các hàng trước đó), ta sử dụng cấu trúc dữ liệu Trie. Điều này giúp cắt tỉa (pruning) các nhánh không hợp lệ cực kỳ hiệu quả, thay vì phải duyệt qua toàn bộ mảng từ vựng ở mỗi bước.

**Mã nguồn Java:**
```java
import java.util.*;

public class WordSquares {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        List<String> startWith = new ArrayList<>();
    }
    
    TrieNode root = new TrieNode();
    List<List<String>> res = new ArrayList<>();
    
    private void buildTrie(String[] words) {
        for (String w : words) {
            TrieNode node = root;
            for (char c : w.toCharArray()) {
                if (node.children[c - 'a'] == null) {
                    node.children[c - 'a'] = new TrieNode();
                }
                node = node.children[c - 'a'];
                node.startWith.add(w);
            }
        }
    }
    
    public List<List<String>> wordSquares(String[] words) {
        buildTrie(words);
        List<String> path = new ArrayList<>();
        for (String w : words) {
            path.add(w);
            backtrack(1, words[0].length(), path);
            path.remove(path.size() - 1);
        }
        return res;
    }
    
    private void backtrack(int step, int len, List<String> path) {
        if (step == len) {
            res.add(new ArrayList<>(path));
            return;
        }
        
        StringBuilder prefix = new StringBuilder();
        for (String s : path) {
            prefix.append(s.charAt(step));
        }
        
        TrieNode node = root;
        for (char c : prefix.toString().toCharArray()) {
            if (node.children[c - 'a'] == null) return;
            node = node.children[c - 'a'];
        }
        
        for (String w : node.startWith) {
            path.add(w);
            backtrack(step + 1, len, path);
            path.remove(path.size() - 1);
        }
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N \cdot 26^L)$ trong đó $N$ là số lượng từ, $L$ là độ dài từ.
- Không gian (Space Complexity): $O(N \cdot L)$ cho cấu trúc Trie và stack đệ quy.

---

## 12. Sudoku Solver (Dancing Links / Backtracking)
**Đề bài chi tiết:**
Giải bài toán Sudoku 9x9 bằng cách điền các số từ 1 đến 9 sao cho mỗi hàng, mỗi cột và mỗi khối 3x3 đều chứa các số không trùng lặp.

**Phân tích thuật toán:**
Để giải hiệu quả, có thể dùng thuật toán Algorithm X của Knuth thông qua cấu trúc Dancing Links (DLX) để chuyển bài toán về dạng Exact Cover. Tuy nhiên, cách triển khai chuẩn Backtracking tối ưu (có thể kết hợp Bitmask hoặc vòng lặp đơn giản) với chiến thuật cắt tỉa tốt (tìm ô còn trống và kiểm tra hợp lệ) cũng đủ nhanh cho lưới 9x9. Dưới đây là mã nguồn Backtracking kinh điển dễ đọc và bảo trì.

**Mã nguồn Java:**
```java
public class SudokuSolver {
    public void solveSudoku(char[][] board) {
        solve(board);
    }
    
    private boolean solve(char[][] board) {
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                if (board[i][j] == '.') {
                    for (char c = '1'; c <= '9'; c++) {
                        if (isValid(board, i, j, c)) {
                            board[i][j] = c;
                            if (solve(board)) return true;
                            board[i][j] = '.'; // Backtrack
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
            if (board[i][col] != '.' && board[i][col] == c) return false;
            if (board[row][i] != '.' && board[row][i] == c) return false;
            if (board[3 * (row / 3) + i / 3][3 * (col / 3) + i % 3] != '.' && 
                board[3 * (row / 3) + i / 3][3 * (col / 3) + i % 3] == c) return false;
        }
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O((9!)^9)$ trong trường hợp xấu nhất, nhưng nhờ hạn chế luật Sudoku nên thời gian thực thi rất nhỏ.
- Không gian (Space Complexity): $O(81) \approx O(1)$ không gian ngăn xếp gọi đệ quy.

---

## 13. Matchsticks to Square
**Đề bài chi tiết:**
Cho mảng số nguyên biểu diễn độ dài các que diêm. Không thể bẻ gãy que diêm, hãy xác định xem có thể sử dụng toàn bộ các que diêm để ghép thành một hình vuông hay không.

**Phân tích thuật toán:**
Tổng độ dài que diêm chia hết cho 4 thì mới có thể tạo thành hình vuông. Ta dùng một mảng `sides` gồm 4 phần tử. Sắp xếp que diêm giảm dần (Heuristic) để ưu tiên đặt các que lớn trước, giúp các nhánh đệ quy sai bị loại bỏ sớm (Pruning). Thử đặt que diêm hiện tại vào 1 trong 4 cạnh, nếu không thỏa mãn, thì Backtrack. Bỏ qua các cạnh có độ dài trùng lặp để tránh trùng nhánh tìm kiếm (Symmetry Breaking).

**Mã nguồn Java:**
```java
import java.util.*;

public class MatchsticksToSquare {
    public boolean makesquare(int[] matchsticks) {
        if (matchsticks == null || matchsticks.length < 4) return false;
        int sum = 0;
        for (int m : matchsticks) sum += m;
        if (sum % 4 != 0) return false;
        
        Arrays.sort(matchsticks);
        // Đảo ngược mảng để sắp xếp giảm dần
        for (int i = 0, j = matchsticks.length - 1; i < j; i++, j--) {
            int temp = matchsticks[i]; matchsticks[i] = matchsticks[j]; matchsticks[j] = temp;
        }
        
        int[] sides = new int[4];
        return backtrack(matchsticks, sides, 0, sum / 4);
    }
    
    private boolean backtrack(int[] nums, int[] sides, int index, int target) {
        if (index == nums.length) {
            return sides[0] == target && sides[1] == target && sides[2] == target;
        }
        
        for (int i = 0; i < 4; i++) {
            if (sides[i] + nums[index] > target) continue;
            // Symmetry breaking (cắt tỉa trùng lặp trạng thái cạnh)
            if (i > 0 && sides[i] == sides[i - 1]) continue;
            
            sides[i] += nums[index];
            if (backtrack(nums, sides, index + 1, target)) return true;
            sides[i] -= nums[index];
        }
        return false;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(4^N)$, vì mỗi que diêm có 4 lựa chọn, nhưng cắt tỉa giảm thiểu đáng kể số vòng lặp.
- Không gian (Space Complexity): $O(N)$ bộ nhớ cho call stack.

---

## 14. Contain Virus
**Đề bài chi tiết:**
Cho một lưới ô vuông, trong đó 1 là vùng nhiễm virus, 0 là an toàn. Mỗi đêm virus lây lan sang các ô kề cạnh. Bạn có thể xây các tường rào (wall) quanh một vùng virus để cô lập nó. Tính tổng số tường cần xây để ngăn chặn hoàn toàn việc lây lan, biết mỗi ngày ta chỉ có thể cô lập vùng virus đe dọa nhiều ô an toàn nhất.

**Phân tích thuật toán:**
Đây là bài toán mô phỏng kết hợp DFS/BFS (Simulation + Search). Mỗi ngày:
1. Dùng DFS tìm tất cả các "vùng virus". Với mỗi vùng, tính số ô 0 xung quanh (frontier) và số tường cần xây (perimeter).
2. Xác định vùng có số lượng frontier lớn nhất.
3. Cộng perimeter của vùng nguy hiểm nhất vào tổng kết quả, đánh dấu vùng đó là đã kiểm soát (`-1`).
4. Các vùng còn lại được lây lan 1 bước sang các ô 0 (đổi thành 1).
Quá trình lặp lại đến khi không còn ô 0 nào bị đe dọa.

**Mã nguồn Java:**
```java
import java.util.*;

public class ContainVirus {
    int[][] dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
    
    public int containVirus(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int ans = 0;
        
        while (true) {
            boolean[][] visited = new boolean[m][n];
            List<Set<Integer>> regions = new ArrayList<>();
            List<Set<Integer>> frontiers = new ArrayList<>();
            List<Integer> perimeters = new ArrayList<>();
            
            for (int r = 0; r < m; r++) {
                for (int c = 0; c < n; c++) {
                    if (grid[r][c] == 1 && !visited[r][c]) {
                        Set<Integer> region = new HashSet<>();
                        Set<Integer> frontier = new HashSet<>();
                        int[] perimeter = new int[]{0};
                        dfs(grid, r, c, visited, region, frontier, perimeter);
                        regions.add(region);
                        frontiers.add(frontier);
                        perimeters.add(perimeter[0]);
                    }
                }
            }
            
            if (regions.isEmpty()) break;
            
            int threatIndex = 0;
            for (int i = 1; i < frontiers.size(); i++) {
                if (frontiers.get(i).size() > frontiers.get(threatIndex).size()) {
                    threatIndex = i;
                }
            }
            
            if (frontiers.get(threatIndex).isEmpty()) break;
            
            ans += perimeters.get(threatIndex);
            for (int i = 0; i < regions.size(); i++) {
                if (i == threatIndex) {
                    for (int code : regions.get(i)) grid[code / n][code % n] = -1; // Cô lập
                } else {
                    for (int code : frontiers.get(i)) grid[code / n][code % n] = 1; // Lây lan
                }
            }
        }
        return ans;
    }
    
    private void dfs(int[][] grid, int r, int c, boolean[][] visited, Set<Integer> region, Set<Integer> frontier, int[] perimeter) {
        int m = grid.length, n = grid[0].length;
        visited[r][c] = true;
        region.add(r * n + c);
        for (int[] d : dirs) {
            int nr = r + d[0], nc = c + d[1];
            if (nr >= 0 && nr < m && nc >= 0 && nc < n) {
                if (grid[nr][nc] == 1 && !visited[nr][nc]) {
                    dfs(grid, nr, nc, visited, region, frontier, perimeter);
                } else if (grid[nr][nc] == 0) {
                    frontier.add(nr * n + nc);
                    perimeter[0]++;
                }
            }
        }
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O((M \cdot N)^{\frac{4}{3}})$ xấp xỉ do tính chất loang và giảm ô. 
- Không gian (Space Complexity): $O(M \cdot N)$ cho bộ nhớ DFS và lưu trữ các set.

---

## 15. Cat and Mouse
**Đề bài chi tiết:**
Trò chơi trên đồ thị vô hướng: Chuột xuất phát từ đỉnh 1, Mèo xuất phát từ đỉnh 2. Đỉnh 0 là lỗ chuột. Chuột đi trước, có thể di chuyển đến đỉnh kề, Mèo đi sau, không được vào đỉnh 0. Trả về 1 nếu Chuột thắng (vào đỉnh 0), 2 nếu Mèo thắng (bắt được chuột), 0 nếu Hòa (trò chơi kéo dài vô tận).

**Phân tích thuật toán:**
Dùng Backtracking kết hợp với thuật toán Minimax và Memoization.
Trạng thái được định nghĩa là `(mouse, cat, turns)`. Để tránh lặp vô hạn (Hòa), nếu `turns` vượt quá $2 \cdot N$ thì ta có thể kết luận là hòa do nguyên lý chuồng bồ câu. Ở mỗi lượt của Chuột, cố gắng tìm đường trả về 1, nếu không thì trả về 0. Lượt của Mèo tìm đường trả về 2.

**Mã nguồn Java:**
```java
import java.util.*;

public class CatAndMouse {
    public int catMouseGame(int[][] graph) {
        int n = graph.length;
        int[][][] memo = new int[n][n][2 * n];
        for (int[][] m2 : memo) for (int[] m1 : m2) Arrays.fill(m1, -1);
        return dfs(graph, 1, 2, 0, memo);
    }
    
    private int dfs(int[][] graph, int m, int c, int t, int[][][] memo) {
        if (t == graph.length * 2) return 0; // Hòa nếu đi quá vòng
        if (m == 0) return 1; // Chuột thắng
        if (m == c) return 2; // Mèo thắng
        if (memo[m][c][t] != -1) return memo[m][c][t];
        
        boolean mouseTurn = (t % 2 == 0);
        int res;
        
        if (mouseTurn) {
            res = 2; // Khởi tạo Chuột thua
            for (int next : graph[m]) {
                int nextState = dfs(graph, next, c, t + 1, memo);
                if (nextState == 1) return memo[m][c][t] = 1; // Tìm được cách Chuột thắng
                if (nextState == 0) res = 0; // Cố gắng cầm hòa nếu không thể thắng
            }
        } else {
            res = 1; // Khởi tạo Mèo thua
            for (int next : graph[c]) {
                if (next == 0) continue; // Mèo không được vào lỗ
                int nextState = dfs(graph, m, next, t + 1, memo);
                if (nextState == 2) return memo[m][c][t] = 2; // Mèo thắng
                if (nextState == 0) res = 0;
            }
        }
        return memo[m][c][t] = res;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N^3)$ với $N$ là số đỉnh do giới hạn số lượng trạng thái trong Memoization.
- Không gian (Space Complexity): $O(N^3)$ để lưu mảng `memo`.

---

## 16. Path with Maximum Gold
**Đề bài chi tiết:**
Trong một mỏ vàng (lưới $M \times N$), mỗi ô có một lượng vàng. Bạn bắt đầu ở bất kỳ ô nào có vàng và đi theo 4 hướng. Không được ghé qua một ô có lượng vàng = 0, và không được thăm lại một ô đã đi qua. Tìm số vàng lớn nhất có thể thu được.

**Phân tích thuật toán:**
Dùng Backtracking cơ bản trên Grid. 
Duyệt qua tất cả các ô trên lưới, nếu ô đó có vàng, gọi hàm đệ quy để lan tỏa ra 4 hướng. Sử dụng biến đổi trạng thái trực tiếp trên mảng `grid` để đánh dấu ô đã thăm bằng cách gán `grid[r][c] = 0`, sau đó Backtrack trả lại giá trị cũ.

**Mã nguồn Java:**
```java
public class PathWithMaxGold {
    int maxGold = 0;
    int[][] dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
    
    public int getMaximumGold(int[][] grid) {
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[0].length; j++) {
                if (grid[i][j] > 0) {
                    backtrack(grid, i, j, 0);
                }
            }
        }
        return maxGold;
    }
    
    private void backtrack(int[][] grid, int r, int c, int currentGold) {
        currentGold += grid[r][c];
        maxGold = Math.max(maxGold, currentGold);
        
        int temp = grid[r][c];
        grid[r][c] = 0; // Đánh dấu đã thăm
        
        for (int[] d : dirs) {
            int nr = r + d[0];
            int nc = c + d[1];
            if (nr >= 0 && nr < grid.length && nc >= 0 && nc < grid[0].length && grid[nr][nc] > 0) {
                backtrack(grid, nr, nc, currentGold);
            }
        }
        
        grid[r][c] = temp; // Backtrack phục hồi trạng thái
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(K \cdot 3^K)$, với $K$ là số ô có vàng (tối đa 25 ô). Tại mỗi ô, trừ ô bắt đầu có 4 hướng, các ô sau đó chỉ có tối đa 3 hướng đi do không đi lùi lại.
- Không gian (Space Complexity): $O(K)$ cho chiều sâu tối đa của ngăn xếp đệ quy.

---

## 17. ZUMA Game
**Đề bài chi tiết:**
Chơi trò Zuma. Cho chuỗi bóng trên bàn `board` và chuỗi bóng trên tay `hand`. Tại mỗi bước, chọn 1 quả bóng từ tay và chèn vào bàn. Nếu có $\ge 3$ quả bóng cùng màu liền kề thì chúng biến mất (xóa dây chuyền). Tìm số bóng ít nhất cần chèn từ tay để xóa sạch bàn, hoặc trả về -1 nếu không thể.

**Phân tích thuật toán:**
Dùng Backtracking kết hợp với đệ quy để quét mọi khả năng.
- Rút gọn trạng thái bàn trước bằng hàm `removeConsecutive`.
- Nếu bàn rỗng, trả về 0.
- Tìm điểm để chèn: Chỉ xét chèn các quả bóng vào cụm bóng cùng màu có sẵn trên bàn. Ta đếm số quả bóng cần để đạt $3$ quả, nếu trên tay có đủ thì trừ đi trên tay và đệ quy, sau đó backtrack trả lại.

**Mã nguồn Java:**
```java
import java.util.*;

public class ZumaGame {
    int maxCount = 6; // Số lượng quả bóng trên tay tối đa là 5
    
    public int findMinStep(String board, String hand) {
        int[] h = new int[26];
        for (char c : hand.toCharArray()) h[c - 'A']++;
        int res = dfs(board + "#", h); // Thêm cờ kết thúc để tính logic ranh giới dễ hơn
        return res == maxCount ? -1 : res;
    }
    
    private int dfs(String board, int[] h) {
        board = removeConsecutive(board);
        if (board.equals("#")) return 0;
        
        int min = maxCount;
        for (int i = 0, j = 0; j < board.length(); j++) {
            if (board.charAt(j) == board.charAt(i)) continue;
            
            int need = 3 - (j - i); // Số bóng cần thêm để đủ 3
            if (h[board.charAt(i) - 'A'] >= need) {
                h[board.charAt(i) - 'A'] -= need;
                int nextSteps = dfs(board.substring(0, i) + board.substring(j), h);
                if (nextSteps != maxCount) min = Math.min(min, nextSteps + need);
                h[board.charAt(i) - 'A'] += need; // Backtrack
            }
            i = j;
        }
        return min;
    }
    
    private String removeConsecutive(String board) {
        for (int i = 0, j = 0; j < board.length(); j++) {
            if (board.charAt(j) == board.charAt(i)) continue;
            if (j - i >= 3) {
                return removeConsecutive(board.substring(0, i) + board.substring(j));
            }
            i = j;
        }
        return board;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(B!)$ nơi $B$ là số lượng bóng trên tay. Kích thước nhỏ nên khả thi.
- Không gian (Space Complexity): $O(B)$ gọi đệ quy.

---

## 18. Optimal Account Balancing
**Đề bài chi tiết:**
Một nhóm người có các giao dịch vay nợ qua lại lẫn nhau. Tìm số lượng giao dịch tối thiểu cần thiết để thanh toán hết mọi nợ nần cho tất cả mọi người.

**Phân tích thuật toán:**
Tính số dư cuối cùng của từng người: `balance = nhận - trả`. Bỏ qua những người có số dư = 0.
Sử dụng DFS Backtracking trên danh sách số dư:
Tìm người đầu tiên chưa cân bằng nợ (`debt != 0`). Duyệt qua các người tiếp theo có số dư khác dấu (để bù trừ). Gán nợ sang người kia, đệ quy xử lý người tiếp theo, sau đó backtrack để thử phương án bù trừ với người khác, tìm ra số bước tối thiểu.

**Mã nguồn Java:**
```java
import java.util.*;

public class OptimalAccountBalancing {
    public int minTransfers(int[][] transactions) {
        Map<Integer, Integer> balances = new HashMap<>();
        for (int[] t : transactions) {
            balances.put(t[0], balances.getOrDefault(t[0], 0) - t[2]);
            balances.put(t[1], balances.getOrDefault(t[1], 0) + t[2]);
        }
        
        List<Integer> debtList = new ArrayList<>();
        for (int b : balances.values()) {
            if (b != 0) debtList.add(b);
        }
        
        return backtrack(0, debtList);
    }
    
    private int backtrack(int index, List<Integer> debts) {
        // Bỏ qua những người đã cân bằng nợ
        while (index < debts.size() && debts.get(index) == 0) index++;
        if (index == debts.size()) return 0;
        
        int minTx = Integer.MAX_VALUE;
        for (int i = index + 1; i < debts.size(); i++) {
            // Thử bù trừ nếu họ có số dư trái dấu (âm trả dương)
            if (debts.get(i) * debts.get(index) < 0) {
                debts.set(i, debts.get(i) + debts.get(index));
                minTx = Math.min(minTx, 1 + backtrack(index + 1, debts));
                debts.set(i, debts.get(i) - debts.get(index)); // Backtrack
            }
        }
        return minTx;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N!)$ nơi $N$ là số lượng người tham gia vào giao dịch có số dư $\neq 0$. $N \le 12$.
- Không gian (Space Complexity): $O(N)$ lưu trữ mảng nợ và call stack.

---

## 19. Graph Coloring (K-colorable)
**Đề bài chi tiết:**
Cho một đồ thị vô hướng và một số nguyên K. Tô màu các đỉnh của đồ thị bằng K màu sao cho không có 2 đỉnh kề nhau nào có cùng màu. Xác định xem có thể tô màu được hay không.

**Phân tích thuật toán:**
Dùng mảng `colors` lưu màu của từng đỉnh.
Backtracking: Xét từng đỉnh, thử tô bằng màu từ $1 \to K$. Viết hàm `isSafe` để kiểm tra màu này có bị trùng với đỉnh kề nào đã tô hay không. Nếu an toàn, đệ quy sang đỉnh tiếp theo. Nếu sai thì backtrack xóa màu.

**Mã nguồn Java:**
```java
import java.util.*;

public class GraphColoring {
    public boolean graphColoring(List<Integer>[] graph, int k) {
        int n = graph.length;
        int[] colors = new int[n];
        return backtrack(graph, k, colors, 0);
    }
    
    private boolean backtrack(List<Integer>[] graph, int k, int[] colors, int node) {
        if (node == graph.length) return true; // Đã tô màu xong tất cả đỉnh
        
        for (int c = 1; c <= k; c++) {
            if (isSafe(graph, colors, node, c)) {
                colors[node] = c;
                if (backtrack(graph, k, colors, node + 1)) return true;
                colors[node] = 0; // Backtrack
            }
        }
        return false;
    }
    
    private boolean isSafe(List<Integer>[] graph, int[] colors, int node, int c) {
        for (int neighbor : graph[node]) {
            if (colors[neighbor] == c) return false;
        }
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(K^V)$ với $V$ là số đỉnh, trường hợp xấu nhất thử mọi nhánh.
- Không gian (Space Complexity): $O(V)$ cho mảng lưu màu.

---

## 20. Hamiltonian Cycle in Directed Graph
**Đề bài chi tiết:**
Tìm một chu trình đi qua tất cả các đỉnh của đồ thị có hướng đúng 1 lần và quay về đỉnh xuất phát.

**Phân tích thuật toán:**
Bắt đầu từ đỉnh 0, DFS đánh dấu đỉnh đã thăm và lưu vào mảng `path`. Nếu đã thăm đủ $V$ đỉnh và có cạnh từ đỉnh cuối cùng quay lại đỉnh xuất phát (`path[0]`), thì tồn tại chu trình. Đừng quên đánh dấu `visited` và Backtrack tại mỗi nút.

**Mã nguồn Java:**
```java
import java.util.*;

public class HamiltonianCycle {
    public boolean hasHamiltonianCycle(int[][] graph) {
        int n = graph.length;
        int[] path = new int[n];
        Arrays.fill(path, -1);
        boolean[] visited = new boolean[n];
        
        path[0] = 0; // Bắt đầu từ đỉnh 0
        visited[0] = true;
        
        return backtrack(graph, path, visited, 1);
    }
    
    private boolean backtrack(int[][] graph, int[] path, boolean[] visited, int pos) {
        int n = graph.length;
        if (pos == n) {
            // Kiểm tra xem có cạnh từ đỉnh cuối trở về đỉnh xuất phát hay không
            return graph[path[pos - 1]][path[0]] == 1;
        }
        
        for (int v = 1; v < n; v++) {
            if (graph[path[pos - 1]][v] == 1 && !visited[v]) {
                path[pos] = v;
                visited[v] = true;
                
                if (backtrack(graph, path, visited, pos + 1)) return true;
                
                // Backtrack
                path[pos] = -1;
                visited[v] = false;
            }
        }
        return false;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(V!)$ ở cấu trúc đồ thị xấu nhất.
- Không gian (Space Complexity): $O(V)$ cho cấu trúc dữ liệu đệ quy.

---

## 21. Strobogrammatic Number III
**Đề bài chi tiết:**
Một số Strobogrammatic là một số nhìn giống hệt nó khi xoay 180 độ (ví dụ: 69, 88, 818). Cho hai chuỗi số `low` và `high`, hãy tìm số lượng các số Strobogrammatic nằm trong phạm vi từ `low` đến `high` (bao gồm cả hai biên).

**Phân tích thuật toán:**
Do không gian tìm kiếm rất lớn (có thể lên tới $10^{15}$), ta sinh trực tiếp các số Strobogrammatic bằng Backtracking. Ta bắt đầu ghép dần các cặp số từ ngoài vào trong: `("0", "0")`, `("1", "1")`, `("8", "8")`, `("6", "9")`, `("9", "6")`.
Ta sinh các số có độ dài từ `len(low)` đến `len(high)`. Nếu độ dài của số sinh ra bằng với giới hạn độ dài của `low` hoặc `high`, ta cần so sánh chuỗi để kiểm tra tính hợp lệ (xem có nằm trong vùng cho phép không).

**Mã nguồn Java:**
```java
import java.util.*;

public class StrobogrammaticNumberIII {
    char[][] pairs = {{'0', '0'}, {'1', '1'}, {'8', '8'}, {'6', '9'}, {'9', '6'}};
    int count = 0;
    
    public int strobogrammaticInRange(String low, String high) {
        for (int len = low.length(); len <= high.length(); len++) {
            char[] c = new char[len];
            backtrack(low, high, c, 0, len - 1);
        }
        return count;
    }
    
    private void backtrack(String low, String high, char[] c, int left, int right) {
        if (left > right) {
            String s = new String(c);
            if ((s.length() == low.length() && s.compareTo(low) < 0) || 
                (s.length() == high.length() && s.compareTo(high) > 0)) {
                return;
            }
            count++;
            return;
        }
        
        for (char[] p : pairs) {
            c[left] = p[0];
            c[right] = p[1];
            
            // "0" không thể đứng đầu nếu độ dài > 1
            if (c.length != 1 && c[0] == '0') continue;
            // Ở giữa (độ dài lẻ) thì chỉ được điền 0, 1, 8
            if (left == right && p[0] != '0' && p[0] != '1' && p[0] != '8') continue;
            
            backtrack(low, high, c, left + 1, right - 1);
        }
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(5^{L/2})$ với $L$ là độ dài tối đa của `high`.
- Không gian (Space Complexity): $O(L)$ cho mảng char và stack đệ quy.

---

## 22. Android Unlock Patterns
**Đề bài chi tiết:**
Điện thoại Android có màn hình khóa $3 \times 3$ với các chấm từ 1 đến 9. Tìm số lượng các mẫu hình mở khóa hợp lệ có số lượng bước đi (độ dài mẫu) nằm trong khoảng từ `m` đến `n`.
Một quy tắc quan trọng: Điểm kết nối trực tiếp hai điểm $A$ và $B$ không được chứa điểm chưa được thăm ở giữa (ví dụ từ 1 sang 3 phải qua 2, trừ phi 2 đã được thăm).

**Phân tích thuật toán:**
Lập mảng `jumps[10][10]` để lưu điểm trung gian bị chắn giữa 2 điểm bất kỳ (ví dụ `jumps[1][3] = jumps[3][1] = 2`).
Dùng DFS với Backtracking và mảng `visited`. Mỗi bước thử nối điểm hiện tại với điểm tiếp theo từ $1 \to 9$. Điểm tiếp theo hợp lệ nếu: nó chưa được thăm, và (hoặc không có điểm trung gian, hoặc điểm trung gian đã được thăm). Do tính chất đối xứng, số cách xuất phát từ 1 bằng 3 bằng 7 bằng 9; từ 2 bằng 4 bằng 6 bằng 8.

**Mã nguồn Java:**
```java
public class AndroidUnlockPatterns {
    int[][] jumps;
    
    public int numberOfPatterns(int m, int n) {
        jumps = new int[10][10];
        jumps[1][3] = jumps[3][1] = 2;
        jumps[4][6] = jumps[6][4] = 5;
        jumps[7][9] = jumps[9][7] = 8;
        jumps[1][7] = jumps[7][1] = 4;
        jumps[2][8] = jumps[8][2] = 5;
        jumps[3][9] = jumps[9][3] = 6;
        jumps[1][9] = jumps[9][1] = jumps[3][7] = jumps[7][3] = 5;
        
        boolean[] visited = new boolean[10];
        int count = 0;
        
        count += dfs(1, visited, 1, m, n) * 4; // Bắt đầu ở góc
        count += dfs(2, visited, 1, m, n) * 4; // Bắt đầu ở cạnh
        count += dfs(5, visited, 1, m, n);     // Bắt đầu ở giữa
        
        return count;
    }
    
    private int dfs(int num, boolean[] visited, int len, int m, int n) {
        int valid = 0;
        if (len >= m) valid++;
        if (len == n) return valid;
        
        visited[num] = true;
        for (int next = 1; next <= 9; next++) {
            int jump = jumps[num][next];
            if (!visited[next] && (jump == 0 || visited[jump])) {
                valid += dfs(next, visited, len + 1, m, n);
            }
        }
        visited[num] = false; // Backtrack
        
        return valid;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(9!)$, cực nhanh nhờ pruning và đối xứng.
- Không gian (Space Complexity): $O(9)$ mảng lưu trữ trạng thái.

---

## 23. Cracking the Safe (De Bruijn Sequence)
**Đề bài chi tiết:**
Có một két sắt có mật khẩu gồm $n$ chữ số, mỗi chữ số có thể là từ $0$ đến $k-1$. Khi nhập thêm 1 chữ số, hệ thống sẽ kiểm tra $n$ chữ số cuối cùng được nhập. Hãy tìm chuỗi ngắn nhất mà khi gõ, két sẽ chắc chắn được mở (chuỗi chứa tất cả các mật khẩu độ dài $n$ có thể).

**Phân tích thuật toán:**
Đây là bài toán tìm Dãy De Bruijn, tương đương với việc tìm chu trình Euler trên đồ thị có hướng. Các đỉnh là các chuỗi độ dài $n-1$, các cạnh là một chữ số từ $0 \to k-1$. Sử dụng DFS / Backtracking hậu tố kết hợp Greedy: ưu tiên thăm nhánh từ chữ số $k-1$ giảm về $0$, dùng `Set` lưu các trạng thái mật khẩu độ dài $n$ đã thử. Khi quay lui, ta chỉ việc nối chữ số vào mảng kết quả (Hierholzer's Algorithm).

**Mã nguồn Java:**
```java
import java.util.*;

public class CrackingTheSafe {
    public String crackSafe(int n, int k) {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < n - 1; i++) sb.append("0");
        String start = sb.toString();
        
        Set<String> visited = new HashSet<>();
        StringBuilder ans = new StringBuilder();
        
        dfs(start, k, visited, ans);
        ans.append(start);
        
        return ans.toString();
    }
    
    private void dfs(String node, int k, Set<String> visited, StringBuilder ans) {
        for (int i = 0; i < k; i++) {
            String edge = node + i;
            if (!visited.contains(edge)) {
                visited.add(edge);
                String nextNode = edge.substring(1); // Lấy n-1 ký tự cuối
                dfs(nextNode, k, visited, ans);
                ans.append(i);
            }
        }
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(k^n)$ là tổng số mật khẩu có thể.
- Không gian (Space Complexity): $O(k^n)$ cho Hash Set và Stack đệ quy.

---

## 24. Makesquare (Chia mảng thành 4 mảng con có tổng bằng nhau)
**Đề bài chi tiết:**
Cho mảng `nums` chứa các số nguyên dương. Xác định xem có thể chia mảng thành đúng 4 mảng con có tổng các phần tử bằng nhau hay không. Đây là dạng chuẩn hóa của thuật toán chia tập hợp.

**Phân tích thuật toán:**
Đây là bài toán NP-Hard. Tổng tất cả các phần tử phải chia hết cho 4 (mục tiêu).
Sử dụng mảng `buckets` kích thước 4. Sắp xếp mảng ban đầu giảm dần để ưu tiên nhét các số lớn vào thùng trước (Heuristic).
Dùng Backtracking thử nhét số hiện tại vào 1 trong 4 thùng, dùng các kỹ thuật cắt nhánh:
- `buckets[j] + nums[i] > target`: bỏ qua thùng j.
- `buckets[j] == buckets[j - 1]`: bỏ qua nếu thùng trước có tổng bằng thùng hiện tại (Symmetry Break).

**Mã nguồn Java:**
```java
import java.util.*;

public class Makesquare {
    public boolean makesquare(int[] nums) {
        int sum = 0;
        for (int n : nums) sum += n;
        if (sum == 0 || sum % 4 != 0) return false;
        
        int target = sum / 4;
        Arrays.sort(nums);
        // Sắp xếp giảm dần
        for (int i = 0, j = nums.length - 1; i < j; i++, j--) {
            int tmp = nums[i]; nums[i] = nums[j]; nums[j] = tmp;
        }
        
        return backtrack(nums, new int[4], 0, target);
    }
    
    private boolean backtrack(int[] nums, int[] buckets, int index, int target) {
        if (index == nums.length) return true;
        
        for (int i = 0; i < 4; i++) {
            if (buckets[i] + nums[index] > target) continue;
            if (i > 0 && buckets[i] == buckets[i - 1]) continue;
            
            buckets[i] += nums[index];
            if (backtrack(nums, buckets, index + 1, target)) return true;
            buckets[i] -= nums[index]; // Backtrack
        }
        return false;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(4^N)$. 
- Không gian (Space Complexity): $O(N)$ bộ nhớ ngăn xếp.

---

## 25. Expression Add Operators
**Đề bài chi tiết:**
Cho một chuỗi chứa chỉ các chữ số `num` và một số nguyên `target`. Trả về tất cả các cách chèn các toán tử `+`, `-`, `*` vào giữa các chữ số để biểu thức toán học sinh ra có kết quả bằng `target`.

**Phân tích thuật toán:**
Dùng DFS với Backtracking. Xây dựng biểu thức và tính toán giá trị `eval` đồng thời.
Phép nhân là vấn đề phức tạp nhất vì độ ưu tiên cao hơn, nên ta cần lưu lại `prevOperand` - toán hạng cuối cùng tham gia biểu thức.
- Nếu là `+`, giá trị tính: `eval + curr`, toán hạng cuối là `curr`.
- Nếu là `-`, giá trị tính: `eval - curr`, toán hạng cuối là `-curr`.
- Nếu là `*`, giá trị tính: `eval - prevOperand + prevOperand * curr`, toán hạng cuối là `prevOperand * curr`.
Đặc biệt, không cho phép toán hạng có số 0 đứng đầu nếu chiều dài > 1 (như "05").

**Mã nguồn Java:**
```java
import java.util.*;

public class ExpressionAddOperators {
    public List<String> addOperators(String num, int target) {
        List<String> res = new ArrayList<>();
        backtrack(res, new StringBuilder(), num, target, 0, 0, 0);
        return res;
    }
    
    private void backtrack(List<String> res, StringBuilder path, String num, int target, int pos, long eval, long prevOperand) {
        if (pos == num.length()) {
            if (target == eval) res.add(path.toString());
            return;
        }
        
        for (int i = pos; i < num.length(); i++) {
            // Loại bỏ trường hợp số có số 0 ở đầu
            if (i != pos && num.charAt(pos) == '0') break;
            
            long curr = Long.parseLong(num.substring(pos, i + 1));
            int len = path.length();
            
            if (pos == 0) {
                path.append(curr);
                backtrack(res, path, num, target, i + 1, curr, curr);
                path.setLength(len);
            } else {
                path.append("+").append(curr);
                backtrack(res, path, num, target, i + 1, eval + curr, curr);
                path.setLength(len);
                
                path.append("-").append(curr);
                backtrack(res, path, num, target, i + 1, eval - curr, -curr);
                path.setLength(len);
                
                path.append("*").append(curr);
                backtrack(res, path, num, target, i + 1, eval - prevOperand + prevOperand * curr, prevOperand * curr);
                path.setLength(len);
            }
        }
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N \cdot 4^N)$ do tại mỗi khoảng hở có 4 lựa chọn (3 phép toán hoặc nối liền số).
- Không gian (Space Complexity): $O(N)$ lưu trữ đường đi đệ quy.

---

## 26. Partition to K Equal Sum Subsets
**Đề bài chi tiết:**
Mở rộng bài Makesquare, thay vì chia 4, cho một mảng `nums` và một số nguyên `K`, xác định xem mảng có thể chia thành đúng `K` tập con rời rạc có tổng bằng nhau hay không.

**Phân tích thuật toán:**
Để giải bài toán dạng tổng quát K, nếu lặp qua K Bucket giống bài 24 với bộ kiểm tra mảng thì sẽ dễ bị TLE do không gian nhánh quá lớn.
Thay vào đó, ta đổi góc nhìn: Tìm các phần tử hợp lệ tạo thành 1 Subset đầu tiên. Sau khi đạt đủ Subset 1, khởi động lại để tìm Subset 2 từ những phần tử còn dư. Đánh dấu các phần tử đã sử dụng bằng một mảng `used`. 

**Mã nguồn Java:**
```java
import java.util.*;

public class PartitionToKSubsets {
    public boolean canPartitionKSubsets(int[] nums, int k) {
        int sum = 0;
        for (int n : nums) sum += n;
        if (sum % k != 0) return false;
        
        int target = sum / k;
        Arrays.sort(nums);
        boolean[] used = new boolean[nums.length];
        
        // Cắt tỉa: nếu phần tử lớn nhất đã lớn hơn target thì không thể
        if (nums[nums.length - 1] > target) return false;
        
        return backtrack(nums, used, k, 0, target, nums.length - 1);
    }
    
    private boolean backtrack(int[] nums, boolean[] used, int k, int currentSum, int target, int startIdx) {
        if (k == 1) return true; // Các phần còn lại chắc chắn có tổng bằng k
        if (currentSum == target) {
            return backtrack(nums, used, k - 1, 0, target, nums.length - 1); // Bắt đầu tìm subset mới
        }
        
        for (int i = startIdx; i >= 0; i--) {
            if (used[i]) continue;
            if (currentSum + nums[i] > target) continue; // Mảng đã sort nên duyệt từ cuối sẽ gặp số lớn trước
            
            used[i] = true;
            if (backtrack(nums, used, k, currentSum + nums[i], target, i - 1)) return true;
            used[i] = false; // Backtrack
            
            // Cắt nhánh cực kỳ quan trọng
            if (currentSum == 0) return false;
        }
        return false;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(K \cdot 2^N)$ trong trường hợp tồi tệ nhất, cắt tỉa giúp thực tế chạy rất nhanh.
- Không gian (Space Complexity): $O(N)$ lưu vết mảng boolean.

---

## 27. Subsets with Bitmasking DP
**Đề bài chi tiết:**
Cho mảng `nums` gồm các phần tử phân biệt, trả về tất cả các tập con (Power Set). So sánh giữa triển khai Backtracking truyền thống và phương pháp sử dụng Toán tử Bit (Bitmasking).

**Phân tích thuật toán:**
Một mảng có $N$ phần tử sẽ có $2^N$ tập con.
Với Backtracking, ta đệ quy nhúng hoặc bỏ qua phần tử thứ $i$.
Với Bitmasking, mỗi tập con ứng với một số nguyên từ $0$ đến $2^N - 1$. Một bit bật (1) tại vị trí thứ $j$ của số nguyên biểu thị rằng phần tử `nums[j]` có mặt trong tập con đó. Phương pháp Bitmask gọn nhẹ, khử bỏ được đệ quy hoàn toàn.

**Mã nguồn Java:**
```java
import java.util.*;

public class SubsetsBitmask {
    // Phương pháp Bitmasking
    public List<List<Integer>> subsetsBitmask(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        int n = nums.length;
        int maxMask = 1 << n; // 2^n
        
        for (int mask = 0; mask < maxMask; mask++) {
            List<Integer> currentSubset = new ArrayList<>();
            for (int i = 0; i < n; i++) {
                if ((mask & (1 << i)) != 0) {
                    currentSubset.add(nums[i]);
                }
            }
            result.add(currentSubset);
        }
        return result;
    }
    
    // Phương pháp Backtracking so sánh
    public List<List<Integer>> subsetsBacktrack(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(), nums, 0);
        return result;
    }
    
    private void backtrack(List<List<Integer>> result, List<Integer> temp, int[] nums, int start) {
        result.add(new ArrayList<>(temp));
        for (int i = start; i < nums.length; i++) {
            temp.add(nums[i]);
            backtrack(result, temp, nums, i + 1);
            temp.remove(temp.size() - 1);
        }
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N \cdot 2^N)$ ở cả 2 cách, thời gian copy sang tập con.
- Không gian (Space Complexity): Bitmask là $O(1)$ phụ trợ, Backtracking là $O(N)$ phụ trợ. Trả về là $O(N \cdot 2^N)$.

---

## 28. Travelling Salesman Problem (Exact Search)
**Đề bài chi tiết:**
Cho ma trận trọng số $N \times N$, biểu diễn khoảng cách giữa các thành phố. Tìm chu trình đi qua tất cả các thành phố đúng 1 lần và quay về thành phố xuất phát với tổng khoảng cách ngắn nhất. Giới hạn số lượng thành phố $N < 15$.

**Phân tích thuật toán:**
Dùng DFS với Bitmask và Branch & Bound (hoặc DP với Bitmask). 
Mảng trạng thái `mask` dùng để đánh dấu thành phố đã đi qua (vd `1101` là đã qua 0, 2, 3). Nếu dùng đệ quy, ta thử tất cả các thành phố chưa nằm trong `mask`, cộng chi phí và cập nhật trạng thái mới đệ quy, đồng thời cắt nhánh nếu tổng tạm thời đã lớn hơn `min_cost` tìm được trước đó.

**Mã nguồn Java:**
```java
public class TSPBacktracking {
    int minCost = Integer.MAX_VALUE;
    
    public int tsp(int[][] graph) {
        int n = graph.length;
        // Bitmask 1 cho thành phố 0
        backtrack(graph, 1, 0, 0, 1, n);
        return minCost;
    }
    
    private void backtrack(int[][] graph, int mask, int currNode, int currCost, int count, int n) {
        if (count == n && graph[currNode][0] > 0) {
            minCost = Math.min(minCost, currCost + graph[currNode][0]);
            return;
        }
        
        for (int i = 0; i < n; i++) {
            if ((mask & (1 << i)) == 0 && graph[currNode][i] > 0) {
                // Pruning (Branch & Bound)
                if (currCost + graph[currNode][i] >= minCost) continue;
                
                backtrack(graph, mask | (1 << i), i, currCost + graph[currNode][i], count + 1, n);
            }
        }
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N!)$, hoặc tối ưu dùng DP là $O(N^2 2^N)$.
- Không gian (Space Complexity): $O(N)$ cho call stack.

---

## 29. Find the Shortest Superstring
**Đề bài chi tiết:**
Cho mảng chuỗi con, tìm một chuỗi lớn nhất có kích thước nhỏ nhất chứa tất cả các chuỗi này. Điều này tương đương với việc tối đa hóa lượng văn bản xếp chồng lên nhau (overlap).

**Phân tích thuật toán:**
Đây là bài TSP mở rộng. 
Tính mảng `overlap[i][j]`, biểu thị độ dài chuỗi chồng lấp tối đa khi ghép mảng $j$ vào ngay sau mảng $i$.
Dùng Backtracking kết hợp với DP / Memoization (sử dụng Bitmask). Tìm chuỗi nối liên tiếp các mảng sinh ra Overlap cực đại. Lưu vết lại mảng đường đi (`parent`) để xây dựng chuỗi kết quả.
Do code gốc DP khá dài, ta viết dưới dạng Backtracking qua hoán vị kết hợp nhánh tối ưu.

**Mã nguồn Java:**
```java
import java.util.*;

public class ShortestSuperstring {
    int maxOverlap = -1;
    int[] bestPath;
    
    public String shortestSuperstring(String[] words) {
        int n = words.length;
        int[][] overlap = new int[n][n];
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (i != j) {
                    overlap[i][j] = getOverlap(words[i], words[j]);
                }
            }
        }
        
        bestPath = new int[n];
        backtrack(overlap, new int[n], new boolean[n], 0, 0);
        
        return buildString(words, bestPath, overlap);
    }
    
    private void backtrack(int[][] overlap, int[] path, boolean[] used, int idx, int currOverlap) {
        if (idx == overlap.length) {
            if (currOverlap > maxOverlap) {
                maxOverlap = currOverlap;
                bestPath = path.clone();
            }
            return;
        }
        
        for (int i = 0; i < overlap.length; i++) {
            if (!used[i]) {
                path[idx] = i;
                used[i] = true;
                int addedOverlap = (idx == 0) ? 0 : overlap[path[idx - 1]][i];
                backtrack(overlap, path, used, idx + 1, currOverlap + addedOverlap);
                used[i] = false;
            }
        }
    }
    
    private int getOverlap(String a, String b) {
        for (int len = Math.min(a.length(), b.length()); len > 0; len--) {
            if (a.endsWith(b.substring(0, len))) return len;
        }
        return 0;
    }
    
    private String buildString(String[] words, int[] path, int[][] overlap) {
        StringBuilder sb = new StringBuilder(words[path[0]]);
        for (int i = 1; i < path.length; i++) {
            int prev = path[i - 1], curr = path[i];
            sb.append(words[curr].substring(overlap[prev][curr]));
        }
        return sb.toString();
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N! \cdot L)$, Backtracking qua $N!$ hoán vị. Dùng DP giảm còn $O(N^2 2^N)$.
- Không gian (Space Complexity): $O(N^2)$ cho ma trận overlap.

---

## 30. The N-Queens II (Space Optimization)
**Đề bài chi tiết:**
Giống bài 1 (N-Queens), nhưng thay vì lưu trữ danh sách các cấu hình khả thi, hãy chỉ trả về **số lượng** các cách đặt hậu.

**Phân tích thuật toán:**
Dùng DFS kết hợp với 3 số nguyên đóng vai trò như Bitmask để đánh dấu cột, đường chéo trái, đường chéo phải. Trạng thái đệ quy tự nhiên khử bỏ nhu cầu sao chép và cập nhật mảng phụ (`boolean[]`), làm cho Backtracking chạy với tốc độ gần như đạt giới hạn lý thuyết tối đa của bài toán.

**Mã nguồn Java:**
```java
public class NQueensIISpaceOpt {
    int count = 0;
    
    public int totalNQueens(int n) {
        dfs(0, 0, 0, 0, n);
        return count;
    }
    
    private void dfs(int row, int cols, int diag1, int diag2, int n) {
        if (row == n) {
            count++;
            return;
        }
        
        // Vị trí khả dụng là bit 1: Loại bỏ những bit đã bị đánh dấu 1 từ cols, diag1, diag2
        int available = ((1 << n) - 1) & ~(cols | diag1 | diag2);
        
        while (available != 0) {
            // Lấy bit 1 ở vị trí thấp nhất (ngoài cùng bên phải)
            int pos = available & -available;
            
            // Xóa bit vừa lấy
            available -= pos;
            
            // Cập nhật trạng thái các đường chéo:
            // diag1 dịch trái 1 bit vì xuống hàng dòng ảnh hưởng lệch trái
            // diag2 dịch phải 1 bit vì xuống hàng dòng ảnh hưởng lệch phải
            dfs(row + 1, cols | pos, (diag1 | pos) << 1, (diag2 | pos) >> 1, n);
        }
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N!)$, thực thi với bitwise operations siêu nhẹ và nhanh.
- Không gian (Space Complexity): $O(N)$ lưu trữ độ sâu ngăn xếp gọi đệ quy.
