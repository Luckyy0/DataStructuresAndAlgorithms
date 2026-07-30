# Optimization and Pruning - Problems (Tối ưu và Cắt tỉa)

## 1. Combination Sum
**Đề bài chi tiết:** Cho một mảng các số nguyên dương phân biệt `candidates` và một số nguyên `target`. Hãy tìm tất cả các tổ hợp duy nhất của các phần tử trong `candidates` sao cho tổng của chúng bằng `target`. Một số có thể được sử dụng nhiều lần trong một tổ hợp.
**Phân tích thuật toán:** Duyệt qua các phần tử. Sắp xếp mảng để thực hiện kỹ thuật cắt tỉa (pruning): nếu `target` nhỏ hơn phần tử hiện tại, ta dừng sớm vòng lặp vì mảng đã tăng dần. Gọi đệ quy và cho phép chọn lại phần tử hiện tại (không tăng `index`).
**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(candidates); // Hỗ trợ pruning
        backtrack(result, new ArrayList<>(), candidates, target, 0);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> currentList, int[] candidates, int remain, int start) {
        if (remain == 0) {
            result.add(new ArrayList<>(currentList));
            return;
        }
        for (int i = start; i < candidates.length; i++) {
            if (remain - candidates[i] < 0) break; // Pruning: Không xét các số lớn hơn
            currentList.add(candidates[i]);
            // start index giữ nguyên là i vì số có thể dùng nhiều lần
            backtrack(result, currentList, candidates, remain - candidates[i], i);
            currentList.remove(currentList.size() - 1); // Backtrack
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(N^{\frac{T}{M}})$ với $N$ là số phần tử, $T$ là target, $M$ là phần tử nhỏ nhất. Không gian: $O(\frac{T}{M})$ cho đệ quy.

## 2. Combination Sum II
**Đề bài chi tiết:** Cho danh sách các số nguyên `candidates` (có thể chứa số trùng lặp) và số `target`. Tìm tất cả tổ hợp duy nhất sao cho tổng bằng `target`. Mỗi phần tử chỉ được dùng 1 lần trong mỗi tổ hợp.
**Phân tích thuật toán:** Tương tự bài 1 nhưng mỗi phần tử chỉ dùng 1 lần, do đó gọi đệ quy truyền vào chỉ số `i + 1`. Cắt tỉa trùng lặp (Symmetry breaking): dùng `if (i > start && candidates[i] == candidates[i-1]) continue;`.
**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(candidates);
        backtrack(result, new ArrayList<>(), candidates, target, 0);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> currentList, int[] candidates, int remain, int start) {
        if (remain == 0) {
            result.add(new ArrayList<>(currentList));
            return;
        }
        for (int i = start; i < candidates.length; i++) {
            // Pruning trùng lặp 
            if (i > start && candidates[i] == candidates[i - 1]) continue; 
            if (remain - candidates[i] < 0) break; // Pruning tính khả thi
            
            currentList.add(candidates[i]);
            backtrack(result, currentList, candidates, remain - candidates[i], i + 1); // Dịch tới index i+1
            currentList.remove(currentList.size() - 1);
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(2^N)$ trong trường hợp xấu nhất, Không gian: $O(N)$ độ sâu đệ quy lớn nhất.

## 3. Combination Sum III
**Đề bài chi tiết:** Tìm tất cả các tổ hợp gồm `k` số phân biệt từ 1 đến 9 sao cho tổng của chúng bằng `n`.
**Phân tích thuật toán:** Không gian tìm kiếm cố định là các số từ 1 đến 9. Nếu danh sách tạm thời đã đủ `k` phần tử và `n == 0` thì ta ghi nhận. Pruning sớm: Nếu danh sách đã $\ge k$ phần tử hoặc tổng mục tiêu trở thành âm, cắt tỉa ngay.
**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> combinationSum3(int k, int n) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(), k, n, 1);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> currentList, int k, int remain, int start) {
        if (currentList.size() == k && remain == 0) {
            result.add(new ArrayList<>(currentList));
            return;
        }
        // Pruning sớm
        if (currentList.size() >= k || remain < 0) return;
        
        for (int i = start; i <= 9; i++) {
            currentList.add(i);
            backtrack(result, currentList, k, remain - i, i + 1);
            currentList.remove(currentList.size() - 1);
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(C(9, k))$, Không gian: $O(k)$ chiều sâu đệ quy.

## 4. Matchsticks to Square
**Đề bài chi tiết:** Cho một mảng `matchsticks` lưu trữ độ dài của các que diêm. Kiểm tra xem có thể dùng tất cả các que diêm để xếp thành một hình vuông không. Bạn không thể bẻ gãy bất kỳ que diêm nào.
**Phân tích thuật toán:** Tổng độ dài các que phải chia hết cho 4. Cạnh hình vuông có target là `sum / 4`. Backtracking điền các que diêm vào 4 cạnh (4 túi). **Pruning 1:** Sắp xếp mảng giảm dần để que dài được xét trước (tránh fail muộn). **Pruning 2:** Bỏ qua cạnh nếu độ dài cạnh vượt mức target. **Pruning 3:** Nếu 2 cạnh đang có cùng độ dài và cạnh này đã thử thất bại, không thử tiếp cạnh kia.
**Mã nguồn Java:**
```java
class Solution {
    public boolean makesquare(int[] matchsticks) {
        int sum = 0;
        for (int x : matchsticks) sum += x;
        if (sum == 0 || sum % 4 != 0) return false;
        
        Arrays.sort(matchsticks);
        // Đảo ngược mảng để tối ưu hóa pruning (que diêm to xếp trước)
        int left = 0, right = matchsticks.length - 1;
        while (left < right) {
            int temp = matchsticks[left];
            matchsticks[left] = matchsticks[right];
            matchsticks[right] = temp;
            left++; right--;
        }
        return backtrack(matchsticks, new int[4], 0, sum / 4);
    }
    
    private boolean backtrack(int[] matchsticks, int[] sides, int index, int target) {
        if (index == matchsticks.length) return true; // Dùng hết diêm
        
        for (int i = 0; i < 4; i++) {
            if (sides[i] + matchsticks[index] > target) continue; // Pruning
            
            // Pruning: Tối ưu đối xứng (Symmetry breaking)
            boolean duplicate = false;
            for (int j = 0; j < i; j++) {
                if (sides[i] == sides[j]) {
                    duplicate = true;
                    break;
                }
            }
            if (duplicate) continue;
            
            sides[i] += matchsticks[index];
            if (backtrack(matchsticks, sides, index + 1, target)) return true;
            sides[i] -= matchsticks[index];
        }
        return false;
    }
}
```
**Độ phức tạp:** Thời gian: $O(4^N)$, Không gian: $O(N)$ (thực tế nhờ cắt tỉa giảm xuống rất nhiều).

## 5. Partition to K Equal Sum Subsets
**Đề bài chi tiết:** Cho mảng các số nguyên `nums` và một số nguyên `k`. Kiểm tra xem có thể chia mảng thành `k` tập con không giao nhau sao cho các tập con có tổng bằng nhau.
**Phân tích thuật toán:** Tương tự như xếp diêm thành hình vuông, nhưng chia làm `k` phần với target là `sum / k`. Khác biệt cốt lõi là chiến lược pruning mạnh hơn: Nếu một tập (túi) đang ở trạng thái trống `(sum = 0)` mà đặt số vào bị quay lui (fail), thì không cần thử tập trống nào khác nữa, vì các tập trống là tương đương nhau.
**Mã nguồn Java:**
```java
class Solution {
    public boolean canPartitionKSubsets(int[] nums, int k) {
        int sum = 0;
        for (int n : nums) sum += n;
        if (sum % k != 0) return false;
        int target = sum / k;
        
        Arrays.sort(nums); // Sắp xếp tăng dần rồi duyệt từ cuối xuống cho tiện
        int[] sums = new int[k];
        return backtrack(nums, sums, nums.length - 1, target);
    }
    
    private boolean backtrack(int[] nums, int[] sums, int index, int target) {
        if (index < 0) return true;
        int val = nums[index];
        
        for (int i = 0; i < sums.length; i++) {
            if (sums[i] + val <= target) {
                sums[i] += val;
                if (backtrack(nums, sums, index - 1, target)) return true;
                sums[i] -= val;
            }
            // Tối ưu cực mạnh (Pruning rỗng): 
            // Nếu bỏ val vào túi trống đầu tiên (sums[i]==0) mà ko tìm được cấu hình,
            // thì ko cần thử các túi trống khác. Đồng thời val bắt buộc phải vô 1 túi nào đó, 
            // mà túi trống còn fail thì các cấu hình trước đó là sai, phải break ngay.
            if (sums[i] == 0) break;
        }
        return false;
    }
}
```
**Độ phức tạp:** Thời gian: $O(k^N)$ (tối ưu đáng kể nhờ pruning), Không gian: $O(N + k)$.

## 6. Target Sum (Backtracking view)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` và `target`. Xây dựng một biểu thức bằng cách cấp dấu `+` hoặc `-` cho mỗi phần tử để tạo ra kết quả. Trả về số cách để biểu thức đạt giá trị đúng bằng `target`.
**Phân tích thuật toán:** Mặc dù bài này tối ưu nhất là Dynamic Programming, việc cài đặt bằng Backtracking giúp nắm vững cách dựng cây trạng thái. Ta gọi đệ quy duyệt qua từng phần tử, chọn `+` hoặc `-`. **Pruning:** Nếu tổng các phần tử tuyệt đối còn lại không đủ để bù đắp khác biệt giữa `current_sum` và `target`, ta không cần duyệt tiếp.
**Mã nguồn Java:**
```java
class Solution {
    int count = 0;
    
    public int findTargetSumWays(int[] nums, int target) {
        int sum = 0;
        for (int num : nums) sum += Math.abs(num);
        if (Math.abs(target) > sum) return 0; // Pruning tính khả thi
        
        backtrack(nums, target, 0, 0);
        return count;
    }
    
    private void backtrack(int[] nums, int target, int index, int currentSum) {
        if (index == nums.length) {
            if (currentSum == target) count++;
            return;
        }
        // Thử dấu +
        backtrack(nums, target, index + 1, currentSum + nums[index]);
        // Thử dấu -
        backtrack(nums, target, index + 1, currentSum - nums[index]);
    }
}
```
**Độ phức tạp:** Thời gian: $O(2^N)$ (nên kết hợp memoization để đạt $O(N \cdot sum)$), Không gian: $O(N)$.

## 7. Split Array into Fibonacci Sequence
**Đề bài chi tiết:** Cho chuỗi số dạng String. Phân tách nó thành một chuỗi con gồm ít nhất 3 số thoả mãn tính chất của dãy Fibonacci (số thứ 3 bằng tổng 2 số trước). Không chứa số 0 đứng đầu, phần tử không vượt max int.
**Phân tích thuật toán:** Duyệt chiều dài của số để cắt chuỗi. **Pruning:** Cắt tỉa nhánh bắt đầu bằng '0' nhưng có nhiều hơn 1 chữ số. Cắt tỉa nhánh nếu số đang cắt $> Integer.MAX\_VALUE$. So sánh số mới cắt với tổng của 2 số liền trước, lớn hơn thì break (do số dài hơn sẽ càng lớn), bé hơn thì continue.
**Mã nguồn Java:**
```java
class Solution {
    public List<Integer> splitIntoFibonacci(String num) {
        List<Integer> result = new ArrayList<>();
        backtrack(num, result, 0);
        return result;
    }
    
    private boolean backtrack(String s, List<Integer> res, int idx) {
        if (idx == s.length() && res.size() >= 3) return true;
        
        long currNum = 0;
        for (int i = idx; i < s.length(); i++) {
            if (s.charAt(idx) == '0' && i > idx) break; // Số không được có leading zero
            
            currNum = currNum * 10 + (s.charAt(i) - '0');
            if (currNum > Integer.MAX_VALUE) break; // Pruning giới hạn int
            
            int size = res.size();
            if (size >= 2) {
                long sum = (long)res.get(size - 1) + res.get(size - 2);
                if (currNum > sum) break; // Cắt tỉa: Nếu đã lớn hơn tổng thì cắt thêm chữ số cũng chỉ lớn hơn
                if (currNum < sum) continue;
            }
            
            res.add((int)currNum);
            if (backtrack(s, res, i + 1)) return true;
            res.remove(res.size() - 1);
        }
        return false;
    }
}
```
**Độ phức tạp:** Thời gian: $O(10^2 \times N)$ do 2 phần tử đầu tối đa 10 chữ số, Không gian: $O(N)$.

## 8. Path Sum II
**Đề bài chi tiết:** Cho root của một cây nhị phân và một số nguyên `targetSum`. Tìm mọi đường dẫn từ gốc đến lá có tổng giá trị dọc theo đường dẫn đúng bằng `targetSum`.
**Phân tích thuật toán:** Bản chất là Backtracking (DFS) trên cây nhị phân. Không có điều kiện `sum > target` để pruning sớm nếu bài toán có số âm. Tại mỗi node, trừ dần `target` và duyệt xuống hai con. Nhớ quay lui khi lên lại.
**Mã nguồn Java:**
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val; TreeNode left, right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<List<Integer>> pathSum(TreeNode root, int targetSum) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(root, targetSum, new ArrayList<>(), result);
        return result;
    }
    
    private void backtrack(TreeNode node, int target, List<Integer> currentList, List<List<Integer>> result) {
        if (node == null) return;
        
        currentList.add(node.val);
        
        // Base case: Node lá và tổng đã thoả
        if (node.left == null && node.right == null && target == node.val) {
            result.add(new ArrayList<>(currentList));
        } else {
            backtrack(node.left, target - node.val, currentList, result);
            backtrack(node.right, target - node.val, currentList, result);
        }
        
        // Quay lui
        currentList.remove(currentList.size() - 1);
    }
}
```
**Độ phức tạp:** Thời gian: $O(N^2)$ (trong worst case tạo list tốn $O(N)$ cho mỗi đường dẫn), Không gian: $O(H)$.

## 9. Fair Distribution of Cookies
**Đề bài chi tiết:** Cho mảng nguyên dương `cookies`, đại diện cho số bánh trong mỗi túi. Phân chia mảng bánh cho `k` đứa trẻ sao cho "tổng lượng bánh lớn nhất mà một đứa trẻ nhận được là nhỏ nhất" (Minimize the maximum unfairness).
**Phân tích thuật toán:** Backtracking phân phát từng túi bánh. Dùng `minUnfairness` toàn cục. **Pruning 1:** Nếu 1 đứa trẻ vừa nhận thêm túi bánh làm số bánh của nó vượt mức `minUnfairness` tốt nhất từng tìm thấy, bỏ qua ngay (vì kết quả đường này chỉ có thể tệ hơn). **Pruning 2:** Nếu chia túi bánh cho 1 đứa trẻ đang có 0 cái bánh mà không ra nghiệm tốt, thì bỏ qua không thử cho các đứa trẻ đang có 0 bánh khác (tính đối xứng).
**Mã nguồn Java:**
```java
class Solution {
    int minUnfairness = Integer.MAX_VALUE;
    
    public int distributeCookies(int[] cookies, int k) {
        int[] children = new int[k];
        backtrack(cookies, children, 0);
        return minUnfairness;
    }
    
    private void backtrack(int[] cookies, int[] children, int index) {
        if (index == cookies.length) {
            int max = 0;
            for (int c : children) max = Math.max(max, c);
            minUnfairness = Math.min(minUnfairness, max);
            return;
        }
        
        for (int i = 0; i < children.length; i++) {
            // Pruning 1: Bỏ qua nếu nhánh này vô dụng
            if (children[i] + cookies[index] >= minUnfairness) continue;
            
            children[i] += cookies[index];
            backtrack(cookies, children, index + 1);
            children[i] -= cookies[index];
            
            // Pruning 2: Tối ưu đối xứng. Đứa trẻ này đang không có bánh, 
            // chia thử mà fail thì chia cho đứa trống tiếp theo cũng thế.
            if (children[i] == 0) break;
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(K^N)$ (tối ưu rất nhanh nhờ đối xứng), Không gian: $O(K + N)$.

## 10. Maximum Compatibility Score Sum
**Đề bài chi tiết:** Ghép $N$ sinh viên với $N$ mentor. Mức độ tương thích giữa cặp sinh viên $i$ và mentor $j$ bằng số câu trả lời khảo sát giống nhau. Tìm tổng điểm tương thích tối đa có thể đạt được.
**Phân tích thuật toán:** Tính điểm của mọi cặp sinh viên-mentor và lưu vào mảng `score[m][m]`. Dùng Backtracking duyệt mọi hoán vị ghép cặp, lưu kết quả tối đa. Pruning trong Backtracking không dễ (nếu dùng DFS trơn thì $O(N!)$), với $N \le 8$ thì tốc độ này chấp nhận được.
**Mã nguồn Java:**
```java
class Solution {
    int maxScore = 0;
    
    public int maxCompatibilitySum(int[][] students, int[][] mentors) {
        int m = students.length;
        int[][] score = new int[m][m];
        
        // Tính ma trận điểm số
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < m; j++) {
                int s = 0;
                for (int k = 0; k < students[0].length; k++) {
                    if (students[i][k] == mentors[j][k]) s++;
                }
                score[i][j] = s;
            }
        }
        
        boolean[] visitedMentor = new boolean[m];
        backtrack(score, visitedMentor, 0, 0);
        return maxScore;
    }
    
    private void backtrack(int[][] score, boolean[] visitedMentor, int studentIdx, int currentScore) {
        if (studentIdx == score.length) {
            maxScore = Math.max(maxScore, currentScore);
            return;
        }
        
        for (int j = 0; j < visitedMentor.length; j++) {
            if (!visitedMentor[j]) {
                visitedMentor[j] = true;
                backtrack(score, visitedMentor, studentIdx + 1, currentScore + score[studentIdx][j]);
                visitedMentor[j] = false; // backtrack
            }
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(N!)$, Không gian: $O(N)$ (cải thiện hơn nếu dùng DP + Bitmask).

---

## 11. Word Boggle (Boggle Game)
**Đề bài chi tiết:** Cho một bảng các ký tự (boggle board) kích thước $M \times N$ và một danh sách các từ. Hãy tìm tất cả các từ trong danh sách có thể được tạo ra bằng cách đi qua các ô liền kề (ngang, dọc, chéo) trên bảng. Mỗi ô chỉ được sử dụng tối đa một lần cho mỗi từ.
**Phân tích thuật toán:** Sử dụng Trie để lưu trữ danh sách các từ. Sau đó duyệt qua từng ô trên bảng và dùng Backtracking (DFS) kết hợp với Trie. Việc sử dụng Trie giúp cắt tỉa (pruning) sớm: nếu chuỗi ký tự hiện tại không phải là tiền tố của bất kỳ từ nào trong Trie, ta dừng duyệt nhánh đó ngay lập tức.
**Mã nguồn Java:**
```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
    String word = null;
}

class Solution {
    public List<String> findWords(char[][] board, String[] words) {
        List<String> result = new ArrayList<>();
        TrieNode root = buildTrie(words);
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                backtrack(board, i, j, root, result);
            }
        }
        return result;
    }

    private void backtrack(char[][] board, int i, int j, TrieNode p, List<String> result) {
        char c = board[i][j];
        if (c == '#' || p.children[c - 'a'] == null) return;
        
        p = p.children[c - 'a'];
        if (p.word != null) {
            result.add(p.word);
            p.word = null; // Tránh trùng lặp kết quả
        }
        
        board[i][j] = '#'; // Đánh dấu ô đã đi qua
        int[][] dirs = {{-1,0}, {1,0}, {0,-1}, {0,1}, {-1,-1}, {-1,1}, {1,-1}, {1,1}};
        for (int[] d : dirs) {
            int ni = i + d[0];
            int nj = j + d[1];
            if (ni >= 0 && ni < board.length && nj >= 0 && nj < board[0].length) {
                backtrack(board, ni, nj, p, result);
            }
        }
        board[i][j] = c; // Backtrack
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
**Độ phức tạp:** Thời gian: $O(M \times N \times 8^L)$, $L$ là độ dài từ tối đa. Không gian: $O(W \times L)$ cho Trie (với $W$ là số từ).

## 12. N-Queens & N-Queens II
**Đề bài chi tiết:** Đặt $N$ quân hậu lên bàn cờ $N \times N$ sao cho không có 2 quân hậu nào tấn công lẫn nhau (không cùng hàng, cột, hoặc đường chéo). Trả về tất cả các cách sắp xếp có thể (hoặc số lượng cách).
**Phân tích thuật toán:** Sử dụng Backtracking điền từng hàng. Thay vì duyệt lại bàn cờ để kiểm tra cột và đường chéo, ta dùng 3 mảng boolean để đánh dấu: cột `cols`, đường chéo chính `d1` (chỉ số `row - col + N`), và đường chéo phụ `d2` (chỉ số `row + col`). Điều này giúp cắt tỉa sớm với thao tác kiểm tra trong $O(1)$.
**Mã nguồn Java:**
```java
class Solution {
    public List<List<String>> solveNQueens(int n) {
        List<List<String>> result = new ArrayList<>();
        char[][] board = new char[n][n];
        for (int i = 0; i < n; i++) Arrays.fill(board[i], '.');
        
        boolean[] cols = new boolean[n];
        boolean[] d1 = new boolean[2 * n];
        boolean[] d2 = new boolean[2 * n];
        
        backtrack(board, 0, cols, d1, d2, result);
        return result;
    }

    private void backtrack(char[][] board, int row, boolean[] cols, boolean[] d1, boolean[] d2, List<List<String>> result) {
        int n = board.length;
        if (row == n) {
            List<String> list = new ArrayList<>();
            for (int i = 0; i < n; i++) list.add(new String(board[i]));
            result.add(list);
            return;
        }
        for (int col = 0; col < n; col++) {
            int id1 = row - col + n;
            int id2 = row + col;
            if (cols[col] || d1[id1] || d2[id2]) continue; // Pruning O(1)
            
            board[row][col] = 'Q';
            cols[col] = d1[id1] = d2[id2] = true;
            
            backtrack(board, row + 1, cols, d1, d2, result);
            
            cols[col] = d1[id1] = d2[id2] = false;
            board[row][col] = '.';
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(N!)$, Không gian: $O(N^2)$ (để lưu bàn cờ).

## 13. Sudoku Solver
**Đề bài chi tiết:** Viết một chương trình để giải một bảng Sudoku $9 \times 9$. Bảng phải được điền các chữ số từ 1-9 sao cho mỗi hàng, mỗi cột và mỗi ô $3 \times 3$ không chứa số lặp lại.
**Phân tích thuật toán:** Quét qua tất cả các ô trống bằng DFS. **Pruning:** Để tối ưu, ta có thể dùng heuristic MRV (điền ô trống có ít khả năng nhất trước) hoặc duy trì mảng `row`, `col`, `box`. Ở phiên bản chuẩn này, ta dùng hàm kiểm tra tính hợp lệ và cắt nhánh lập tức nếu số thử điền vi phạm quy tắc.
**Mã nguồn Java:**
```java
class Solution {
    public void solveSudoku(char[][] board) {
        backtrack(board);
    }

    private boolean backtrack(char[][] board) {
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                if (board[i][j] == '.') {
                    for (char c = '1'; c <= '9'; c++) {
                        if (isValid(board, i, j, c)) {
                            board[i][j] = c;
                            if (backtrack(board)) return true;
                            board[i][j] = '.'; // Backtrack
                        }
                    }
                    return false; // Pruning: không có số nào hợp lệ thì nhánh này sai
                }
            }
        }
        return true;
    }

    private boolean isValid(char[][] board, int row, int col, char c) {
        for (int i = 0; i < 9; i++) {
            if (board[i][col] == c) return false;
            if (board[row][i] == c) return false;
            if (board[3 * (row / 3) + i / 3][3 * (col / 3) + i % 3] == c) return false;
        }
        return true;
    }
}
```
**Độ phức tạp:** Thời gian: $O(9^{81})$ (thực tế rất nhỏ nhờ pruning), Không gian: $O(1)$ hoặc $O(81)$ trên Call Stack.

## 14. Construct the Lexicographically Largest Valid Sequence
**Đề bài chi tiết:** Cho số nguyên `n`, tìm chuỗi dãy số dài $2n - 1$ sao cho: số 1 xuất hiện 1 lần, các số từ 2 đến $n$ xuất hiện 2 lần và khoảng cách giữa 2 lần xuất hiện của số $i$ là đúng $i$. Trả về dãy có thứ tự từ điển lớn nhất.
**Phân tích thuật toán:** Để đảm bảo thứ tự từ điển lớn nhất, ta ưu tiên đặt các số từ $n$ giảm dần xuống 1 vào khoảng trống đầu tiên tìm được. Backtracking duyệt qua các vị trí. **Pruning:** Kiểm tra xem vị trí tương ứng phía sau có trống hay không, nếu không bỏ qua nhánh này.
**Mã nguồn Java:**
```java
class Solution {
    public int[] constructDistancedSequence(int n) {
        int[] res = new int[2 * n - 1];
        boolean[] used = new boolean[n + 1];
        backtrack(res, used, n, 0);
        return res;
    }

    private boolean backtrack(int[] res, boolean[] used, int n, int index) {
        if (index == res.length) return true;
        if (res[index] != 0) return backtrack(res, used, n, index + 1); // Đã có số

        for (int i = n; i >= 1; i--) {
            if (used[i]) continue;
            int nextIndex = (i == 1) ? index : index + i;
            
            // Pruning: Nếu khoảng cách thoả mãn và ô sau còn trống
            if (nextIndex < res.length && res[nextIndex] == 0) {
                res[index] = i;
                res[nextIndex] = i;
                used[i] = true;
                
                if (backtrack(res, used, n, index + 1)) return true;
                
                res[index] = 0; // Backtrack
                res[nextIndex] = 0;
                used[i] = false;
            }
        }
        return false;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N!)$ trong trường hợp xấu nhất, Không gian: $O(N)$.

## 15. Verbal Arithmetic Puzzle
**Đề bài chi tiết:** Cho các từ `words` và `result`. Gán các chữ cái bằng chữ số (0-9) sao cho tổng các từ bằng `result`. Các chữ cái đứng đầu không được gán bằng 0.
**Phân tích thuật toán:** Thay vì Backtracking đoán mù từng chữ cái $O(10!)$ và kiểm tra chuỗi, ta chuyển thành phương trình có trọng số. Ví dụ: `SEND + MORE = MONEY` thành $\sum weight_i \times X_i = 0$. Duyệt đệ quy theo mảng chữ cái. Nhánh nào duy trì được tổng bằng $0$ đến cuối sẽ là đáp án.
**Mã nguồn Java:**
```java
class Solution {
    private static final int[] POW_10 = new int[7];
    
    public boolean isSolvable(String[] words, String result) {
        Set<Character> charSet = new HashSet<>();
        int[] weight = new int[26];
        boolean[] nonZero = new boolean[26];
        
        POW_10[0] = 1;
        for (int i = 1; i < 7; i++) POW_10[i] = POW_10[i - 1] * 10;
        
        // Tính toán trọng số của các chữ cái trong words
        for (String w : words) {
            for (int i = 0; i < w.length(); i++) {
                char c = w.charAt(i);
                charSet.add(c);
                weight[c - 'A'] += POW_10[w.length() - 1 - i];
            }
            if (w.length() > 1) nonZero[w.charAt(0) - 'A'] = true;
        }
        
        // Tính toán trọng số của chữ cái trong result (phải trừ đi)
        for (int i = 0; i < result.length(); i++) {
            char c = result.charAt(i);
            charSet.add(c);
            weight[c - 'A'] -= POW_10[result.length() - 1 - i];
        }
        if (result.length() > 1) nonZero[result.charAt(0) - 'A'] = true;
        
        List<Character> charList = new ArrayList<>(charSet);
        return backtrack(charList, weight, nonZero, new boolean[10], 0, 0);
    }
    
    private boolean backtrack(List<Character> charList, int[] weight, boolean[] nonZero, boolean[] used, int index, int currentSum) {
        if (index == charList.size()) return currentSum == 0;
        
        char c = charList.get(index);
        for (int i = 0; i <= 9; i++) {
            if (used[i]) continue;
            if (i == 0 && nonZero[c - 'A']) continue; // Ký tự đầu không được là 0
            
            used[i] = true;
            if (backtrack(charList, weight, nonZero, used, index + 1, currentSum + weight[c - 'A'] * i)) {
                return true;
            }
            used[i] = false;
        }
        return false;
    }
}
```
**Độ phức tạp:** Thời gian: $O(10!)$ không gian hoán vị, trong Java chạy rất nhanh do $10! \approx 3.6 \times 10^6$. Không gian: $O(1)$.

## 16. Palindrome Partitioning
**Đề bài chi tiết:** Cho chuỗi `s`, phân tách `s` thành các chuỗi con sao cho mọi chuỗi con đều là chuỗi đối xứng (palindrome). Trả về tất cả các cách phân tách có thể.
**Phân tích thuật toán:** Backtracking thử cắt chuỗi ở mọi độ dài có thể. **Pruning sớm:** Thay vì mỗi lần cắt lại dùng vòng lặp để kiểm tra tính đối xứng tốn $O(N)$, ta tính trước ma trận `boolean[][] dp` (isPalindrome) bằng Quy hoạch động trong $O(N^2)$. Nhờ vậy, kiểm tra đối xứng trong vòng đệ quy chỉ tốn $O(1)$.
**Mã nguồn Java:**
```java
class Solution {
    public List<List<String>> partition(String s) {
        int n = s.length();
        boolean[][] dp = new boolean[n][n];
        for (int i = 0; i < n; i++) Arrays.fill(dp[i], true);
        
        // Tiền xử lý O(N^2)
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;
                dp[i][j] = (s.charAt(i) == s.charAt(j)) && dp[i + 1][j - 1];
            }
        }
        
        List<List<String>> result = new ArrayList<>();
        backtrack(s, dp, 0, new ArrayList<>(), result);
        return result;
    }

    private void backtrack(String s, boolean[][] dp, int start, List<String> current, List<List<String>> result) {
        if (start == s.length()) {
            result.add(new ArrayList<>(current));
            return;
        }
        for (int end = start; end < s.length(); end++) {
            if (dp[start][end]) { // Pruning tính đối xứng O(1)
                current.add(s.substring(start, end + 1));
                backtrack(s, dp, end + 1, current, result);
                current.remove(current.size() - 1);
            }
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \cdot 2^N)$, Không gian: $O(N^2)$.

## 17. Generate Parentheses
**Đề bài chi tiết:** Cho `n` cặp dấu ngoặc đơn, viết hàm tạo ra tất cả các tổ hợp ngoặc đơn hợp lệ (mở và đóng ngoặc phải đúng thứ tự).
**Phân tích thuật toán:** Ở mỗi vị trí, ta có 2 lựa chọn: thêm `(` hoặc thêm `)`. **Pruning logic:** Nhánh không hợp lệ được cắt rất sớm. Ta không thêm `(` nếu số lượng ngoặc mở đã bằng `n`; và không thêm `)` nếu số lượng ngoặc đóng đã vượt quá hoặc bằng số lượng ngoặc mở hiện tại.
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
            current.deleteCharAt(current.length() - 1); // Backtrack
        }
        if (close < open) { // Pruning: Chỉ đóng khi ngoặc mở nhiều hơn
            current.append(")");
            backtrack(result, current, open, close + 1, max);
            current.deleteCharAt(current.length() - 1);
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(\frac{4^N}{\sqrt{N}})$ (Tương đương số Catalan thứ $N$), Không gian: $O(N)$ cho đệ quy.

## 18. Letter Combinations of a Phone Number
**Đề bài chi tiết:** Cho một chuỗi chứa các chữ số từ 2-9, trả về tất cả các tổ hợp chữ cái mà số điện thoại có thể đại diện.
**Phân tích thuật toán:** Đề bài yêu cầu liệt kê toàn bộ hoán vị, nên không gian tìm kiếm không có cấu trúc bị ràng buộc logic, không có cơ hội áp dụng Pruning đặc thù. Đây là bài toán cơ bản nhất về cấu trúc Backtracking sinh tổ hợp đầy đủ.
**Mã nguồn Java:**
```java
class Solution {
    private static final String[] MAPPING = {
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

        int digit = digits.charAt(index) - '0';
        String letters = MAPPING[digit];
        for (char c : letters.toCharArray()) {
            current.append(c);
            backtrack(result, current, digits, index + 1);
            current.deleteCharAt(current.length() - 1);
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(4^N \cdot N)$, Không gian: $O(N)$.

## 19. Restore IP Addresses
**Đề bài chi tiết:** Cho một chuỗi số, khôi phục lại tất cả các địa chỉ IPv4 hợp lệ có thể tạo ra bằng cách chèn 3 dấu chấm vào chuỗi (segment từ 0-255 và không có leading zero nếu độ dài $> 1$).
**Phân tích thuật toán:** IP gồm đúng 4 phần (segment). Backtracking thử chia chuỗi. **Pruning:** Nếu số segment đạt 4 mà chưa hết chuỗi, hoặc chưa đạt 4 mà đã hết chuỗi thì bỏ qua. Tại mỗi vị trí, cắt tối đa 3 ký tự. Cắt tỉa thêm nếu segment bắt đầu bằng '0' mà độ dài $>1$, hoặc giá trị $>255$.
**Mã nguồn Java:**
```java
class Solution {
    public List<String> restoreIpAddresses(String s) {
        List<String> result = new ArrayList<>();
        if (s.length() < 4 || s.length() > 12) return result;
        backtrack(result, s, new ArrayList<>(), 0);
        return result;
    }

    private void backtrack(List<String> result, String s, List<String> current, int start) {
        if (current.size() == 4) {
            if (start == s.length()) {
                result.add(String.join(".", current));
            }
            return;
        }

        for (int i = 1; i <= 3; i++) {
            if (start + i > s.length()) break;
            String segment = s.substring(start, start + i);
            
            // Pruning: leading zeros hoặc vượt quá 255
            if ((segment.startsWith("0") && segment.length() > 1) || Integer.parseInt(segment) > 255) {
                continue;
            }

            current.add(segment);
            backtrack(result, s, current, start + i);
            current.remove(current.size() - 1);
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(3^4) = O(1)$, do số bước duyệt bị giới hạn cực hạn bởi tối đa 4 segment x 3 ký tự. Không gian: $O(1)$.

## 20. Subsets & Subsets II
**Đề bài chi tiết:** Cho một mảng `nums`. Tìm tất cả các tập con (Power Set). Ở phiên bản có phần tử trùng lặp (Subsets II), tập con đầu ra không được trùng nhau.
**Phân tích thuật toán:** Sinh tập con bằng cách mỗi lần duyệt đẩy phần tử vào cấu trúc, ngay lập tức ghi nhận cấu hình. Để xử lý trùng lặp, ta cần sắp xếp mảng. **Pruning:** Bỏ qua các nhánh sinh ra tập trùng bằng điều kiện `if (i > start && nums[i] == nums[i - 1]) continue;`.
**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(nums); // Bắt buộc sort để pruning liền kề
        backtrack(result, new ArrayList<>(), nums, 0);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> current, int[] nums, int start) {
        result.add(new ArrayList<>(current)); 

        for (int i = start; i < nums.length; i++) {
            // Pruning: Cắt tỉa nhánh trùng lặp ở cùng tầng (cùng độ sâu)
            if (i > start && nums[i] == nums[i - 1]) continue;
            
            current.add(nums[i]);
            backtrack(result, current, nums, i + 1);
            current.remove(current.size() - 1);
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \cdot 2^N)$, Không gian: $O(N)$ độ sâu đệ quy.

---

## 21. Beautiful Arrangement
**Đề bài chi tiết:** Cho số nguyên `n`. Khởi tạo một mảng chứa các số từ 1 đến `n`. Hãy đếm số hoán vị của mảng sao cho với mọi vị trí $i$ (1-indexed), một trong hai điều kiện sau thỏa mãn:
- `nums[i]` chia hết cho $i$
- $i$ chia hết cho `nums[i]`
**Phân tích thuật toán:** Dùng Backtracking duyệt qua từng vị trí. Khác với sinh hoán vị thông thường, ta chỉ điền một số vào vị trí hiện tại nếu số đó thỏa mãn điều kiện chia hết (Pruning).
**Mã nguồn Java:**
```java
class Solution {
    int count = 0;
    
    public int countArrangement(int n) {
        boolean[] visited = new boolean[n + 1];
        backtrack(n, 1, visited);
        return count;
    }
    
    private void backtrack(int n, int index, boolean[] visited) {
        if (index > n) {
            count++;
            return;
        }
        for (int i = 1; i <= n; i++) {
            if (!visited[i] && (i % index == 0 || index % i == 0)) { // Pruning: Chỉ tiếp tục nếu hợp lệ
                visited[i] = true;
                backtrack(n, index + 1, visited);
                visited[i] = false;
            }
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(K)$ với $K$ là số lượng cấu hình hợp lệ (rất nhỏ so với $N!$). Không gian: $O(N)$.

## 22. Permutations & Permutations II
**Đề bài chi tiết:** Cho một mảng các số nguyên (có thể có phần tử trùng lặp), trả về tất cả các hoán vị duy nhất của chúng.
**Phân tích thuật toán:** Dùng Backtracking sinh hoán vị và mảng boolean `used` để đánh dấu phần tử đã dùng. Để tránh sinh các hoán vị trùng lặp, ta phải sắp xếp mảng ban đầu. **Pruning:** Bỏ qua phần tử trùng lặp bằng cách: nếu `nums[i] == nums[i-1]` và `!used[i-1]`, ta bỏ qua. Điều này ép buộc thứ tự sử dụng các phần tử trùng lặp phải từ trái sang phải.
**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(nums); // Phải sort để nhận diện trùng lặp
        boolean[] used = new boolean[nums.length];
        backtrack(result, new ArrayList<>(), nums, used);
        return result;
    }
    
    private void backtrack(List<List<Integer>> result, List<Integer> current, int[] nums, boolean[] used) {
        if (current.size() == nums.length) {
            result.add(new ArrayList<>(current));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            if (used[i]) continue;
            
            // Pruning: Cắt nhánh sinh hoán vị trùng lặp
            if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) continue;
            
            used[i] = true;
            current.add(nums[i]);
            backtrack(result, current, nums, used);
            used[i] = false;
            current.remove(current.size() - 1);
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \cdot N!)$, Không gian: $O(N)$.

## 23. Word Search
**Đề bài chi tiết:** Cho một bảng các ký tự $M \times N$ và một chuỗi `word`. Trả về `true` nếu `word` tồn tại trong bảng (được tạo bởi các ô kề cạnh ngang hoặc dọc, không dùng lại ô).
**Phân tích thuật toán:** Dùng DFS từ mỗi ô trên bảng. **Pruning:** Nếu ký tự tại ô hiện tại khác với ký tự tương ứng của `word` hoặc ô đã được thăm, ta quay lui ngay. Hơn nữa, nếu hàm đệ quy trả về `true` từ một hướng, ta cắt nhánh tìm kiếm ở các hướng còn lại.
**Mã nguồn Java:**
```java
class Solution {
    public boolean exist(char[][] board, String word) {
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                if (backtrack(board, word, i, j, 0)) return true;
            }
        }
        return false;
    }
    
    private boolean backtrack(char[][] board, String word, int i, int j, int index) {
        if (index == word.length()) return true;
        
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length || board[i][j] != word.charAt(index)) {
            return false; // Pruning
        }
        
        char temp = board[i][j];
        board[i][j] = '#'; // Mark as visited
        
        boolean found = backtrack(board, word, i + 1, j, index + 1) ||
                        backtrack(board, word, i - 1, j, index + 1) ||
                        backtrack(board, word, i, j + 1, index + 1) ||
                        backtrack(board, word, i, j - 1, index + 1);
                        
        board[i][j] = temp; // Backtrack
        return found;
    }
}
```
**Độ phức tạp:** Thời gian: $O(M \cdot N \cdot 3^L)$ ($L$ là độ dài từ, chỉ có 3 hướng mở rộng từ ô tiếp theo), Không gian: $O(L)$.

## 24. Word Search II
**Đề bài chi tiết:** Giống bài Word Search nhưng tìm kiếm một danh sách các từ.
**Phân tích thuật toán:** Tối ưu hóa bằng cách dùng Trie (Cây tiền tố) để lưu danh sách từ. Duyệt bảng một lần bằng DFS, truyền theo node của Trie. **Pruning 1:** Cắt nhánh nếu Trie không có nhánh tiền tố tương ứng. **Pruning 2:** Sau khi tìm thấy một từ, ta xóa từ đó khỏi Trie (`p.word = null`) để tránh duyệt lại và tìm trùng lặp.
**Mã nguồn Java:**
```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
    String word;
}

class Solution {
    public List<String> findWords(char[][] board, String[] words) {
        TrieNode root = new TrieNode();
        for (String w : words) {
            TrieNode p = root;
            for (char c : w.toCharArray()) {
                if (p.children[c - 'a'] == null) p.children[c - 'a'] = new TrieNode();
                p = p.children[c - 'a'];
            }
            p.word = w;
        }
        
        List<String> result = new ArrayList<>();
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                backtrack(board, i, j, root, result);
            }
        }
        return result;
    }
    
    private void backtrack(char[][] board, int i, int j, TrieNode p, List<String> result) {
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length || board[i][j] == '#') return;
        
        char c = board[i][j];
        if (p.children[c - 'a'] == null) return; // Pruning: Không có tiền tố
        p = p.children[c - 'a'];
        
        if (p.word != null) {
            result.add(p.word);
            p.word = null; // Pruning: Tránh tìm lại
        }
        
        board[i][j] = '#'; // Mark visited
        backtrack(board, i + 1, j, p, result);
        backtrack(board, i - 1, j, p, result);
        backtrack(board, i, j + 1, p, result);
        backtrack(board, i, j - 1, p, result);
        board[i][j] = c; // Backtrack
    }
}
```
**Độ phức tạp:** Thời gian: $O(M \cdot N \cdot 4^L)$, Không gian: $O(W \cdot L)$ cho Trie.

## 25. Optimal Account Balancing
**Đề bài chi tiết:** Cho danh sách các giao dịch (người $A$ đưa người $B$ số tiền $X$). Tìm số lượng giao dịch tối thiểu cần thực hiện để thanh toán mọi khoản nợ sao cho số dư của tất cả mọi người bằng 0.
**Phân tích thuật toán:** Trước tiên, tính số dư cuối cùng của từng người. Lọc bỏ những người có số dư bằng 0, chỉ giữ lại những người còn nợ hoặc chủ nợ vào mảng `debt`. Quy về dùng Backtracking để ghép người nợ với chủ nợ. **Pruning 1:** Bỏ qua người có dư nợ bằng 0. **Pruning 2:** Chỉ thử thanh toán chéo khi hai người trái dấu nhau (một bên nợ, một bên chủ nợ).
**Mã nguồn Java:**
```java
class Solution {
    public int minTransfers(int[][] transactions) {
        Map<Integer, Integer> balances = new HashMap<>();
        for (int[] t : transactions) {
            balances.put(t[0], balances.getOrDefault(t[0], 0) - t[2]);
            balances.put(t[1], balances.getOrDefault(t[1], 0) + t[2]);
        }
        
        List<Integer> debt = new ArrayList<>();
        for (int amount : balances.values()) {
            if (amount != 0) debt.add(amount);
        }
        
        return backtrack(0, debt);
    }
    
    private int backtrack(int index, List<Integer> debt) {
        while (index < debt.size() && debt.get(index) == 0) index++; // Pruning người đã cân bằng
        if (index == debt.size()) return 0;
        
        int minTransactions = Integer.MAX_VALUE;
        for (int i = index + 1; i < debt.size(); i++) {
            // Chỉ giao dịch khi hai bên trái dấu nhau
            if (debt.get(index) * debt.get(i) < 0) {
                debt.set(i, debt.get(i) + debt.get(index));
                minTransactions = Math.min(minTransactions, 1 + backtrack(index + 1, debt));
                debt.set(i, debt.get(i) - debt.get(index)); // Backtrack
            }
        }
        return minTransactions;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N!)$ trong đó $N$ là số người có nợ, Không gian: $O(N)$.

## 26. Non-decreasing Subsequences
**Đề bài chi tiết:** Cho mảng số nguyên `nums`, tìm tất cả các dãy con tăng hoặc không giảm có độ dài ít nhất là 2.
**Phân tích thuật toán:** Khác với Subsets II, ở bài này ta không được phép sắp xếp mảng ban đầu vì sẽ làm phá vỡ thứ tự gốc của dãy con. Do đó, để cắt tỉa các dãy con trùng lặp, ta sử dụng một `HashSet` cục bộ (chỉ tồn tại trong nhánh rẽ của tầng đệ quy hiện tại) để lưu vết những phần tử đã được đưa vào nhánh con.
**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> findSubsequences(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(), nums, 0);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> current, int[] nums, int start) {
        if (current.size() >= 2) {
            result.add(new ArrayList<>(current));
        }
        
        Set<Integer> usedInLevel = new HashSet<>(); // HashSet cục bộ cho tầng đệ quy
        for (int i = start; i < nums.length; i++) {
            // Pruning: Bỏ qua nếu nhỏ hơn phần tử cuối hoặc đã xét
            if (!current.isEmpty() && current.get(current.size() - 1) > nums[i]) continue;
            if (usedInLevel.contains(nums[i])) continue; // Chống trùng lặp không cần sort
            
            usedInLevel.add(nums[i]);
            current.add(nums[i]);
            backtrack(result, current, nums, i + 1);
            current.remove(current.size() - 1);
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(2^N)$, Không gian: $O(N)$.

## 27. Number of Squareful Arrays
**Đề bài chi tiết:** Trả về số lượng hoán vị của mảng `nums` sao cho tổng của mọi cặp phần tử liền kề đều là số chính phương.
**Phân tích thuật toán:** Bài toán sinh hoán vị độc nhất (Subsets II / Permutations II) kết hợp với điều kiện cặp kề nhau. **Pruning 1:** Sắp xếp mảng để tránh sinh hoán vị trùng. **Pruning 2:** Trong vòng lặp sinh, nếu phần tử trước đó cộng với phần tử đang xét không phải là số chính phương thì dừng nhánh tìm kiếm.
**Mã nguồn Java:**
```java
class Solution {
    int count = 0;
    
    public int numSquarefulPerms(int[] nums) {
        Arrays.sort(nums);
        boolean[] used = new boolean[nums.length];
        backtrack(nums, used, new ArrayList<>());
        return count;
    }
    
    private void backtrack(int[] nums, boolean[] used, List<Integer> current) {
        if (current.size() == nums.length) {
            count++;
            return;
        }
        
        for (int i = 0; i < nums.length; i++) {
            if (used[i]) continue;
            if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) continue; // Pruning trùng lặp
            
            // Pruning tổng chính phương
            if (!current.isEmpty()) {
                int sum = current.get(current.size() - 1) + nums[i];
                int root = (int) Math.sqrt(sum);
                if (root * root != sum) continue;
            }
            
            used[i] = true;
            current.add(nums[i]);
            backtrack(nums, used, current);
            used[i] = false;
            current.remove(current.size() - 1);
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(N!)$, Không gian: $O(N)$.

## 28. Matchsticks to Square (Bitmask)
**Đề bài chi tiết:** Giống bài 4, nhưng yêu cầu một cách tiếp cận sử dụng Quy hoạch động kết hợp Masking thay cho Backtracking Pruning trực tiếp.
**Phân tích thuật toán:** Dùng một số nguyên `mask` để biểu diễn các que diêm đã được dùng. Tính chiều dài đã lấp của toàn bộ cấu hình, modulo cho `target` để xem cạnh hiện tại đang có chiều dài bao nhiêu. Ta lưu kết quả của từng trạng thái `mask` vào Memoization table để không tính lại.
**Mã nguồn Java:**
```java
class Solution {
    public boolean makesquare(int[] matchsticks) {
        int sum = 0;
        for (int x : matchsticks) sum += x;
        if (sum == 0 || sum % 4 != 0) return false;
        int target = sum / 4;
        
        return backtrack(matchsticks, 0, 0, target, new Boolean[1 << matchsticks.length]);
    }
    
    private boolean backtrack(int[] matchsticks, int mask, int currentLen, int target, Boolean[] memo) {
        if (mask == (1 << matchsticks.length) - 1) return true; // Dùng hết diêm
        if (memo[mask] != null) return memo[mask];
        
        for (int i = 0; i < matchsticks.length; i++) {
            if ((mask & (1 << i)) == 0) { // Nếu diêm thứ i chưa dùng
                if (currentLen + matchsticks[i] <= target) {
                    int nextLen = (currentLen + matchsticks[i]) % target;
                    if (backtrack(matchsticks, mask | (1 << i), nextLen, target, memo)) {
                        return memo[mask] = true;
                    }
                }
            }
        }
        return memo[mask] = false;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \cdot 2^N)$, Không gian: $O(2^N)$.

## 29. Find Minimum Time to Finish All Jobs
**Đề bài chi tiết:** Cho mảng thời gian các công việc `jobs`, phân công chúng cho `k` công nhân sao cho thời gian làm việc tối đa của một công nhân là nhỏ nhất.
**Phân tích thuật toán:** Tương tự như Cookie Distribution (bài 9). **Pruning 1:** Sắp xếp công việc giảm dần. Tại mỗi bước chia việc, nếu thời gian công nhân bị vượt qua `minTime` tốt nhất thì bỏ qua nhánh. **Pruning 2:** Tối ưu đối xứng: Nếu phân công vào người đang trống mà thất bại, ta break nhánh (không thử người trống khác).
**Mã nguồn Java:**
```java
class Solution {
    int res = Integer.MAX_VALUE;

    public int minimumTimeRequired(int[] jobs, int k) {
        Arrays.sort(jobs);
        int i = 0, j = jobs.length - 1;
        while (i < j) { // Đảo ngược mảng
            int temp = jobs[i]; jobs[i] = jobs[j]; jobs[j] = temp;
            i++; j--;
        }
        backtrack(jobs, new int[k], 0, 0);
        return res;
    }

    private void backtrack(int[] jobs, int[] workers, int index, int currentMax) {
        if (currentMax >= res) return; // Pruning 1
        if (index == jobs.length) {
            res = currentMax;
            return;
        }

        for (int i = 0; i < workers.length; i++) {
            workers[i] += jobs[index];
            backtrack(jobs, workers, index + 1, Math.max(currentMax, workers[i]));
            workers[i] -= jobs[index];
            
            // Pruning 2: Tối ưu đối xứng cho những người công nhân rỗng
            if (workers[i] == 0) break; 
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(K^N)$, Không gian: $O(N + K)$.

## 30. Permutation Sequence
**Đề bài chi tiết:** Cho `n` và `k`, tìm hoán vị thứ `k` của dãy từ 1 đến `n` (được sắp xếp theo thứ tự từ điển).
**Phân tích thuật toán:** Dùng Backtracking sinh đến đúng $K$ hoán vị sẽ bị TLE vì giới hạn. Thay vào đó, ta sử dụng toán học để bỏ qua cấu hình. Mỗi khi chọn 1 chữ số ở vị trí đầu tiên, ta có $(n-1)!$ hoán vị. Do đó, ta có thể xác định trực tiếp chữ số tại mỗi vị trí dựa vào phép chia lấy nguyên `k / factorial[n - i]`.
**Mã nguồn Java:**
```java
class Solution {
    public String getPermutation(int n, int k) {
        List<Integer> numbers = new ArrayList<>();
        int[] factorial = new int[n + 1];
        
        factorial[0] = 1;
        for (int i = 1; i <= n; i++) {
            factorial[i] = factorial[i - 1] * i;
            numbers.add(i);
        }
        
        k--; // Chuyển k về dạng 0-indexed
        StringBuilder sb = new StringBuilder();
        
        for (int i = 1; i <= n; i++) {
            int index = k / factorial[n - i];
            sb.append(numbers.get(index));
            numbers.remove(index); // Bỏ phần tử đã dùng
            k = k % factorial[n - i];
        }
        
        return sb.toString();
    }
}
```
**Độ phức tạp:** Thời gian: $O(N^2)$ (do thao tác remove trong danh sách tốn $O(N)$), Không gian: $O(N)$.
