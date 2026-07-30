# 04 - Advanced Bit Tricks - Problems

## 1. Bitwise AND of Numbers Range (LeetCode 201)

**Đề bài chi tiết:**
Cho hai số nguyên `left` và `right` đại diện cho một khoảng `[left, right]`. Hãy trả về phép toán bitwise AND của tất cả các số trong khoảng này, bao gồm cả hai đầu mút.

**Phân tích thuật toán:**
Bản chất của phép AND liên tiếp là nếu có bất kỳ một số nào trong khoảng có bit `0` tại vị trí `i`, thì bit `i` của kết quả sẽ là `0`. Khi khoảng từ `left` đến `right` khác nhau, các bit bị thay đổi sẽ nằm ở phần hậu tố. Ta chỉ cần tìm **tiền tố chung (common prefix)** của biểu diễn nhị phân của `left` và `right`. Các bit còn lại ở bên phải sẽ trở thành `0`.

**Mã nguồn Java:**
```java
public class Solution {
    public int rangeBitwiseAnd(int left, int right) {
        int shiftCount = 0;
        // Dịch phải cho đến khi 2 số bằng nhau (tìm tiền tố chung)
        while (left < right) {
            left >>= 1;
            right >>= 1;
            shiftCount++;
        }
        // Dịch ngược lại để khôi phục các số 0 ở hậu tố
        return left << shiftCount;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(1)$ hoặc $O(\log(\text{MAX\_INT})) = O(32)$, tối đa là 32 lần vòng lặp.
- **Không gian:** $O(1)$.

---

## 2. Total Hamming Distance (LeetCode 477)

**Đề bài chi tiết:**
Cho một mảng số nguyên `nums`. Bạn hãy tính tổng khoảng cách Hamming giữa mọi cặp phần tử trong mảng. (Khoảng cách Hamming giữa hai số là số lượng các vị trí bit mà chúng khác nhau).

**Phân tích thuật toán:**
Thay vì so sánh từng cặp mất $O(N^2)$, ta xét độc lập từng vị trí bit từ 0 đến 31. Đối với vị trí bit thứ `i`, nếu có `C` số trong mảng có bit đó là `1`, thì số lượng số có bit đó là `0` sẽ là `N - C`. Khoảng cách Hamming tại bit thứ `i` đóng góp vào tổng sẽ là `C * (N - C)`.

**Mã nguồn Java:**
```java
public class Solution {
    public int totalHammingDistance(int[] nums) {
        int total = 0;
        int n = nums.length;
        
        for (int i = 0; i < 32; i++) {
            int countOnes = 0;
            for (int num : nums) {
                countOnes += (num >> i) & 1;
            }
            total += countOnes * (n - countOnes);
        }
        
        return total;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N \times 32) \approx O(N)$.
- **Không gian:** $O(1)$.

---

## 3. Power of Four (LeetCode 342)

**Đề bài chi tiết:**
Cho một số nguyên `n`. Hãy trả về `true` nếu nó là lũy thừa của 4, nếu không trả về `false`.

**Phân tích thuật toán:**
Một số là lũy thừa của 4 phải thỏa mãn 3 điều kiện:
1. Phải là số dương (`n > 0`).
2. Phải là lũy thừa của 2 (chỉ có duy nhất 1 bit `1`), dùng `(n & (n - 1)) == 0`.
3. Bit `1` duy nhất phải nằm ở vị trí chẵn (tính từ 0). Ta có thể kiểm tra bằng một mask `0x55555555` (có dạng nhị phân là `...01010101`).

**Mã nguồn Java:**
```java
public class Solution {
    public boolean isPowerOfFour(int n) {
        // Kiểm tra > 0, lũy thừa 2, và có cấu trúc bit phù hợp
        return n > 0 && (n & (n - 1)) == 0 && (n & 0x55555555) != 0;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(1)$.
- **Không gian:** $O(1)$.

---

## 4. Minimum One Bit Operations to Make Integers Zero (LeetCode 1611)

**Đề bài chi tiết:**
Cho số nguyên `n`. Có 2 thao tác có thể thực hiện trên biểu diễn nhị phân của `n`:
- Đổi bit thứ 0 (bên phải cùng).
- Đổi bit thứ `i` nếu bit thứ `i-1` là 1 và các bit từ `i-2` xuống 0 đều là 0.
Tìm số bước ít nhất để biến `n` thành 0.

**Phân tích thuật toán:**
Bài toán này liên quan chặt chẽ với mã Gray (Gray Code). Số thao tác tối thiểu để đưa `n` về 0 chính là việc đảo ngược hàm chuyển một số sang mã Gray. Gọi hàm nghịch đảo là `revGray`. Công thức đảo mã Gray là `ans = n ^ (n >> 1) ^ (n >> 2) ...`.

**Mã nguồn Java:**
```java
public class Solution {
    public int minimumOneBitOperations(int n) {
        int ans = n;
        ans ^= ans >> 16;
        ans ^= ans >> 8;
        ans ^= ans >> 4;
        ans ^= ans >> 2;
        ans ^= ans >> 1;
        return ans;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(1)$ (do chỉ dịch bit tối đa 5 bước với số nguyên 32 bit).
- **Không gian:** $O(1)$.

---

## 5. Valid Word Abbreviation (LeetCode 408)

**Đề bài chi tiết:**
Một chuỗi có thể được viết tắt bằng cách thay một lượng kí tự liên tiếp bằng độ dài của chúng. (Không được có số `0` ở đầu). Hãy kiểm tra xem chuỗi viết tắt có khớp với chuỗi gốc hay không.

**Phân tích thuật toán:**
Sử dụng hai con trỏ, một duyệt qua chuỗi gốc, một duyệt qua chuỗi viết tắt. Khi gặp ký tự số trong chuỗi viết tắt, tạo một số nguyên (xử lý cẩn thận `0` đứng đầu). Nhảy con trỏ chuỗi gốc một lượng bằng số đó. (Bài này có thể không thuần Bit manipulation nhưng thường gộp chung trong kỹ năng xử lý mảng/chuỗi ở cấp độ ký tự).

**Mã nguồn Java:**
```java
public class Solution {
    public boolean validWordAbbreviation(String word, String abbr) {
        int i = 0, j = 0;
        while (i < word.length() && j < abbr.length()) {
            if (Character.isDigit(abbr.charAt(j))) {
                if (abbr.charAt(j) == '0') return false; // Không có số 0 ở đầu
                int num = 0;
                while (j < abbr.length() && Character.isDigit(abbr.charAt(j))) {
                    num = num * 10 + (abbr.charAt(j) - '0');
                    j++;
                }
                i += num;
            } else {
                if (word.charAt(i) != abbr.charAt(j)) return false;
                i++;
                j++;
            }
        }
        return i == word.length() && j == abbr.length();
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N)$ với $N$ là độ dài của chuỗi.
- **Không gian:** $O(1)$.

---

## 6. Maximum XOR of Two Numbers in an Array (LeetCode 421)

**Đề bài chi tiết:**
Cho mảng số nguyên `nums`. Trả về giá trị cực đại của `nums[i] XOR nums[j]`.

**Phân tích thuật toán:**
Dùng Bitwise Trie. Duyệt từ bit 31 về 0, với mỗi số `num`, ta muốn tối đa hóa XOR nên tại mỗi bit ta sẽ cố gắng đi về nhánh ngược lại (nghĩa là bit `1 - (num >> i & 1)`). Nếu nhánh đó tồn tại, ta sẽ cộng $2^i$ vào kết quả XOR. Nếu không, ta đành đi vào nhánh còn lại.

**Mã nguồn Java:**
```java
public class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[2];
    }
    
    public int findMaximumXOR(int[] nums) {
        TrieNode root = new TrieNode();
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
        
        int max = 0;
        for (int num : nums) {
            TrieNode curr = root;
            int currSum = 0;
            for (int i = 31; i >= 0; i--) {
                int bit = (num >> i) & 1;
                int toggled = 1 - bit;
                if (curr.children[toggled] != null) {
                    currSum += (1 << i);
                    curr = curr.children[toggled];
                } else {
                    curr = curr.children[bit];
                }
            }
            max = Math.max(max, currSum);
        }
        return max;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N)$.
- **Không gian:** $O(N)$.

---

## 7. Maximum XOR With an Element From Array (LeetCode 1707)

**Đề bài chi tiết:**
Cho mảng `nums` và danh sách các truy vấn `queries = [[xi, mi]]`. Trả về mảng đáp án trong đó mỗi truy vấn cần tìm `xi XOR nums[j]` lớn nhất sao cho `nums[j] <= mi`. Nếu không có phần tử nào thoả mãn, trả về -1.

**Phân tích thuật toán:**
Sắp xếp mảng `nums` và các truy vấn theo `mi` tăng dần (nhớ lưu lại chỉ số gốc của truy vấn). Khi duyệt các truy vấn, ta nạp các phần tử `nums[j] <= mi` vào Bitwise Trie. Sau đó thực hiện tìm Max XOR tương tự bài trước.

**Mã nguồn Java:**
```java
import java.util.Arrays;

public class Solution {
    class Trie {
        Trie[] children = new Trie[2];
        public void insert(int num) {
            Trie curr = this;
            for (int i = 31; i >= 0; i--) {
                int bit = (num >> i) & 1;
                if (curr.children[bit] == null) curr.children[bit] = new Trie();
                curr = curr.children[bit];
            }
        }
        public int getMax(int num) {
            if (this.children[0] == null && this.children[1] == null) return -1;
            Trie curr = this;
            int max = 0;
            for (int i = 31; i >= 0; i--) {
                int bit = (num >> i) & 1;
                int toggled = 1 - bit;
                if (curr.children[toggled] != null) {
                    max += (1 << i);
                    curr = curr.children[toggled];
                } else {
                    curr = curr.children[bit];
                }
            }
            return max;
        }
    }

    public int[] maximizeXor(int[] nums, int[][] queries) {
        Arrays.sort(nums);
        int n = queries.length;
        int[][] qWithIdx = new int[n][3];
        for (int i = 0; i < n; i++) {
            qWithIdx[i][0] = queries[i][0];
            qWithIdx[i][1] = queries[i][1];
            qWithIdx[i][2] = i;
        }
        Arrays.sort(qWithIdx, (a, b) -> a[1] - b[1]);
        
        int[] ans = new int[n];
        Trie trie = new Trie();
        int idx = 0;
        
        for (int[] q : qWithIdx) {
            while (idx < nums.length && nums[idx] <= q[1]) {
                trie.insert(nums[idx++]);
            }
            ans[q[2]] = trie.getMax(q[0]);
        }
        return ans;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N \log N + Q \log Q + (N+Q) \times 32)$.
- **Không gian:** $O(N \times 32 + Q)$.

---

## 8. Shortest Path Visiting All Nodes (LeetCode 847)

**Đề bài chi tiết:**
Cho đồ thị vô hướng. Bạn có thể bắt đầu từ bất kì đỉnh nào, có thể đi lại cạnh và đỉnh nhiều lần. Tìm độ dài đường đi ngắn nhất để thăm tất cả các đỉnh.

**Phân tích thuật toán:**
Dùng BFS kết hợp Bitmask để lưu trữ trạng thái. Trạng thái của bài toán gồm có `[node_hiện_tại, bitmask_đã_thăm]`. `bitmask` sử dụng $N$ bit, trong đó nếu bit thứ $i$ bằng 1 nghĩa là đã thăm đỉnh $i$. Trạng thái đích là bitmask có tất cả $N$ bit đều là 1.

**Mã nguồn Java:**
```java
import java.util.LinkedList;
import java.util.Queue;

public class Solution {
    public int shortestPathLength(int[][] graph) {
        int n = graph.length;
        if (n == 1) return 0;
        
        int finalState = (1 << n) - 1;
        Queue<int[]> q = new LinkedList<>();
        boolean[][] visited = new boolean[n][1 << n];
        
        for (int i = 0; i < n; i++) {
            q.offer(new int[]{i, 1 << i, 0});
            visited[i][1 << i] = true;
        }
        
        while (!q.isEmpty()) {
            int[] curr = q.poll();
            int node = curr[0], mask = curr[1], dist = curr[2];
            
            for (int neighbor : graph[node]) {
                int nextMask = mask | (1 << neighbor);
                if (nextMask == finalState) return dist + 1;
                
                if (!visited[neighbor][nextMask]) {
                    visited[neighbor][nextMask] = true;
                    q.offer(new int[]{neighbor, nextMask, dist + 1});
                }
            }
        }
        return -1;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N \times 2^N)$.
- **Không gian:** $O(N \times 2^N)$.

---

## 9. Count Triplets That Can Form Two Arrays of Equal XOR (LeetCode 1442)

**Đề bài chi tiết:**
Cho mảng `arr`. Chọn bộ ba `(i, j, k)` sao cho $0 \le i < j \le k < \text{arr.length}$. Gọi $a = \text{arr}[i] \oplus ... \oplus \text{arr}[j-1]$ và $b = \text{arr}[j] \oplus ... \oplus \text{arr}[k]$. Đếm số bộ ba sao cho $a = b$.

**Phân tích thuật toán:**
$a = b \implies a \oplus b = 0$. Có nghĩa là XOR của đoạn từ $i$ đến $k$ bằng 0: $\text{arr}[i] \oplus ... \oplus \text{arr}[k] = 0$.
Nếu ta có đoạn $[i, k]$ như vậy, thì với MỌI $j$ nằm giữa $i < j \le k$, điều kiện $a = b$ đều thoả mãn. Có $k - i$ cách chọn $j$. Do đó, ta chỉ cần tìm các đoạn có tổng XOR bằng 0.

**Mã nguồn Java:**
```java
public class Solution {
    public int countTriplets(int[] arr) {
        int n = arr.length;
        int[] prefix = new int[n + 1];
        for (int i = 0; i < n; i++) {
            prefix[i + 1] = prefix[i] ^ arr[i];
        }
        
        int count = 0;
        for (int i = 0; i < n; i++) {
            for (int k = i + 1; k < n; k++) {
                if (prefix[i] == prefix[k + 1]) {
                    count += (k - i);
                }
            }
        }
        return count;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N^2)$.
- **Không gian:** $O(N)$.

---

## 10. Minimum Number of Flips to Convert Binary Matrix to Zero Matrix (LeetCode 1284)

**Đề bài chi tiết:**
Cho một ma trận nhị phân `mat`. Một bước flip ở ô $(r, c)$ sẽ đổi giá trị của ô đó và 4 ô kề cạnh. Tìm số bước flip nhỏ nhất để ma trận trở thành toàn số 0.

**Phân tích thuật toán:**
Do kích thước của `mat` rất nhỏ (tối đa 3x3), tổng số ô chỉ là $M \times N \le 9$. Ta có thể biểu diễn toàn bộ trạng thái của ma trận bằng một bitmask tối đa 9 bit. Sử dụng BFS để tìm đường đi ngắn nhất từ trạng thái bắt đầu tới trạng thái đích (0).

**Mã nguồn Java:**
```java
import java.util.LinkedList;
import java.util.Queue;

public class Solution {
    public int minFlips(int[][] mat) {
        int m = mat.length, n = mat[0].length;
        int start = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (mat[i][j] == 1) start |= (1 << (i * n + j));
            }
        }
        if (start == 0) return 0;
        
        Queue<Integer> q = new LinkedList<>();
        boolean[] visited = new boolean[1 << (m * n)];
        q.offer(start);
        visited[start] = true;
        
        int steps = 0;
        int[] dirs = {0, 0, 1, 0, -1, 0, 0, 1, 0, -1}; // (0,0) bao gồm chính nó
        
        while (!q.isEmpty()) {
            int size = q.size();
            for (int k = 0; k < size; k++) {
                int curr = q.poll();
                if (curr == 0) return steps;
                
                for (int i = 0; i < m; i++) {
                    for (int j = 0; j < n; j++) {
                        int nextState = curr;
                        for (int d = 0; d < 5; d++) {
                            int r = i + dirs[d * 2], c = j + dirs[d * 2 + 1];
                            if (r >= 0 && r < m && c >= 0 && c < n) {
                                nextState ^= (1 << (r * n + c));
                            }
                        }
                        if (!visited[nextState]) {
                            visited[nextState] = true;
                            q.offer(nextState);
                        }
                    }
                }
            }
            steps++;
        }
        return -1;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(2^{MN} \times MN)$.
- **Không gian:** $O(2^{MN})$.

---

## 11. Subsets (LeetCode 78)

**Đề bài chi tiết:**
Cho một mảng số nguyên `nums` chứa các phần tử phân biệt. Hãy trả về tất cả các tập con có thể có (powerset). Tập hợp kết quả không được chứa các tập con trùng lặp. Bạn có thể trả về các tập con theo bất kỳ thứ tự nào.

**Phân tích thuật toán:**
Một mảng có $N$ phần tử sẽ có $2^N$ tập con. Ta có thể dùng các số nguyên từ $0$ đến $2^N - 1$ để biểu diễn các tập con này. Mỗi số nguyên có $N$ bit, trong đó bit thứ $i$ bằng 1 nghĩa là phần tử `nums[i]` có mặt trong tập con đó, ngược lại là không. Ta chỉ cần duyệt qua mọi bitmask từ $0$ đến $2^N - 1$, ứng với mỗi bitmask, kiểm tra các bit và lấy các phần tử tương ứng thêm vào tập con.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        int n = nums.length;
        int totalSubsets = 1 << n; // 2^n
        
        for (int mask = 0; mask < totalSubsets; mask++) {
            List<Integer> subset = new ArrayList<>();
            for (int i = 0; i < n; i++) {
                // Kiểm tra bit thứ i của mask có phải là 1 không
                if ((mask & (1 << i)) != 0) {
                    subset.add(nums[i]);
                }
            }
            result.add(subset);
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N \times 2^N)$.
- **Không gian:** $O(N \times 2^N)$ để lưu trữ kết quả.

---

## 12. Single Number II (LeetCode 137)

**Đề bài chi tiết:**
Cho một mảng số nguyên `nums`, trong đó mọi phần tử đều xuất hiện chính xác 3 lần, ngoại trừ một phần tử duy nhất xuất hiện đúng 1 lần. Hãy tìm phần tử duy nhất đó. Bạn phải triển khai một thuật toán với độ phức tạp thời gian tuyến tính và chỉ sử dụng không gian phụ $O(1)$.

**Phân tích thuật toán:**
Do các phần tử (ngoại trừ số cần tìm) xuất hiện 3 lần, tổng số lượng bit `1` tại bất kỳ vị trí `i` nào của tất cả các số sẽ là bội số của 3, cộng thêm bit của số xuất hiện 1 lần. 
Vì vậy, ta đếm số lượng bit `1` tại từng vị trí từ 0 đến 31 của tất cả các phần tử. Sau đó, lấy tổng số bit `1` tại mỗi vị trí modulo 3. Kết quả dư chính là bit của phần tử xuất hiện 1 lần.

**Mã nguồn Java:**
```java
public class Solution {
    public int singleNumber(int[] nums) {
        int ans = 0;
        for (int i = 0; i < 32; i++) {
            int sum = 0;
            for (int num : nums) {
                sum += (num >> i) & 1;
            }
            // Nếu sum % 3 == 1, nghĩa là bit thứ i của số duy nhất là 1
            if (sum % 3 != 0) {
                ans |= (1 << i);
            }
        }
        return ans;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N \times 32) = O(N)$.
- **Không gian:** $O(1)$.

---

## 13. Single Number III (LeetCode 260)

**Đề bài chi tiết:**
Cho mảng số nguyên `nums`, trong đó có đúng hai phần tử xuất hiện duy nhất 1 lần, các phần tử còn lại xuất hiện đúng 2 lần. Hãy tìm hai phần tử xuất hiện 1 lần đó. Trả về kết quả theo bất kỳ thứ tự nào. Phải có thời gian chạy tuyến tính và bộ nhớ $O(1)$.

**Phân tích thuật toán:**
Đầu tiên, ta tính tổng XOR của toàn bộ mảng. Kết quả này chính là $A \oplus B$, với $A$ và $B$ là hai số cần tìm.
Vì $A \neq B$, chắc chắn $A \oplus B \neq 0$, tức là có ít nhất một bit bằng 1. Ta tìm bit 1 thấp nhất của kết quả này bằng `diff = xor & (-xor)`.
Dựa vào bit `diff`, ta chia các số trong mảng thành hai nhóm: một nhóm có bit tại vị trí `diff` bằng 1 (chứa một số cần tìm), nhóm kia bằng 0 (chứa số cần tìm còn lại). XOR toàn bộ từng nhóm ta sẽ được $A$ và $B$.

**Mã nguồn Java:**
```java
public class Solution {
    public int[] singleNumber(int[] nums) {
        int bitmask = 0;
        for (int num : nums) {
            bitmask ^= num;
        }
        
        // Tìm bit 1 ở vị trí thấp nhất
        int diff = bitmask & (-bitmask);
        
        int x = 0;
        for (int num : nums) {
            // Chia mảng thành 2 nhóm dựa trên bit diff
            if ((num & diff) != 0) {
                x ^= num;
            }
        }
        
        return new int[]{x, bitmask ^ x};
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N)$.
- **Không gian:** $O(1)$.

---

## 14. Missing Number (LeetCode 268)

**Đề bài chi tiết:**
Cho một mảng `nums` gồm $n$ số nguyên phân biệt trong khoảng từ $0$ đến $n$. Hãy trả về số duy nhất trong khoảng đó bị thiếu trong mảng.

**Phân tích thuật toán:**
Dựa vào tính chất $x \oplus x = 0$. Ta có thể XOR tất cả các phần tử trong mảng `nums` với tất cả các số từ $0$ đến $n$. Mọi phần tử xuất hiện trong mảng sẽ bị triệt tiêu do có mặt 2 lần (một lần trong mảng, một lần ở khoảng $[0, n]$). Số không có trong mảng sẽ không bị triệt tiêu và chính là kết quả cuối cùng.

**Mã nguồn Java:**
```java
public class Solution {
    public int missingNumber(int[] nums) {
        int missing = nums.length;
        for (int i = 0; i < nums.length; i++) {
            missing ^= i ^ nums[i];
        }
        return missing;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N)$.
- **Không gian:** $O(1)$.

---

## 15. Find the Difference (LeetCode 389)

**Đề bài chi tiết:**
Cho hai chuỗi `s` và `t`. Chuỗi `t` được tạo ra bằng cách trộn ngẫu nhiên chuỗi `s` và sau đó thêm một ký tự ngẫu nhiên vào một vị trí bất kỳ. Hãy tìm ký tự đã được thêm vào chuỗi `t`.

**Phân tích thuật toán:**
Tương tự như bài Missing Number, ta có thể xem mỗi ký tự là một số nguyên (mã ASCII). Ký tự được thêm vào sẽ xuất hiện số lần lẻ, trong khi các ký tự khác (trong cả `s` và `t`) xuất hiện số lần chẵn. Do đó, XOR tất cả các ký tự trong cả hai chuỗi sẽ triệt tiêu các ký tự giống nhau, để lại mã ASCII của ký tự được thêm vào.

**Mã nguồn Java:**
```java
public class Solution {
    public char findTheDifference(String s, String t) {
        char c = 0;
        for (int i = 0; i < s.length(); i++) {
            c ^= s.charAt(i);
        }
        for (int i = 0; i < t.length(); i++) {
            c ^= t.charAt(i);
        }
        return c;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N)$ với $N$ là độ dài của chuỗi.
- **Không gian:** $O(1)$.

---

## 16. Number of 1 Bits (LeetCode 191)

**Đề bài chi tiết:**
Viết một hàm nhận vào một số nguyên không dấu (unsigned integer) và trả về số lượng bit `1` có trong biểu diễn nhị phân của nó (còn được gọi là trọng lượng Hamming).

**Phân tích thuật toán:**
Áp dụng thuật toán Brian Kernighan: phép toán `n & (n - 1)` sẽ luôn xóa đi bit `1` nằm ở vị trí thấp nhất của `n`. Ta chỉ cần lặp lại phép toán này và đếm số bước cho đến khi `n = 0`. Số bước chính là số lượng bit `1`.

**Mã nguồn Java:**
```java
public class Solution {
    // Treat n as an unsigned value
    public int hammingWeight(int n) {
        int count = 0;
        while (n != 0) {
            n &= (n - 1);
            count++;
        }
        return count;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(k)$, với $k$ là số lượng bit `1` của $n$. Tối đa là $O(32)$.
- **Không gian:** $O(1)$.

---

## 17. Reverse Bits (LeetCode 190)

**Đề bài chi tiết:**
Đảo ngược các bit của một số nguyên không dấu 32 bit.

**Phân tích thuật toán:**
Ta dùng một biến `ans` khởi tạo bằng 0. Duyệt 32 lần (vì số nguyên có 32 bit). Ở mỗi bước:
1. Dịch trái `ans` đi 1 bit để lấy chỗ trống: `ans <<= 1`.
2. Lấy bit cuối cùng của `n`: `n & 1`, sau đó cộng hoặc `OR` với `ans`: `ans |= (n & 1)`.
3. Dịch phải `n` đi 1 bit (dịch không dấu logic): `n >>>= 1`.

**Mã nguồn Java:**
```java
public class Solution {
    // Treat n as an unsigned value
    public int reverseBits(int n) {
        int ans = 0;
        for (int i = 0; i < 32; i++) {
            ans <<= 1;
            ans |= (n & 1);
            n >>>= 1;
        }
        return ans;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(1)$ (cố định 32 vòng lặp).
- **Không gian:** $O(1)$.

---

## 18. Bitwise ORs of Subarrays (LeetCode 898)

**Đề bài chi tiết:**
Cho mảng số nguyên `arr`. Ta có thể chọn bất kỳ mảng con liên tiếp nào và tính giá trị bitwise OR của tất cả các phần tử trong đó. Hãy trả về số lượng các giá trị OR khác nhau có thể thu được.

**Phân tích thuật toán:**
Gọi `cur` là tập hợp các giá trị OR của tất cả các mảng con kết thúc tại chỉ số `i`. Khi chuyển sang `i+1`, tập hợp giá trị mới `next` sẽ bao gồm `arr[i+1]` và `x | arr[i+1]` với mọi `x` trong `cur`. 
Do tính chất của OR, số lượng bit 1 chỉ có thể tăng lên, nên kích thước của `cur` tại mỗi bước sẽ không vượt quá 32. Ta dùng một `HashSet` tổng `ans` để lưu mọi kết quả gặp được.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int subarrayBitwiseORs(int[] arr) {
        Set<Integer> ans = new HashSet<>();
        Set<Integer> cur = new HashSet<>();
        
        for (int x : arr) {
            Set<Integer> next = new HashSet<>();
            next.add(x);
            for (int y : cur) {
                next.add(x | y);
            }
            ans.addAll(next);
            cur = next;
        }
        
        return ans.size();
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N \times 32) = O(N)$. Tại mỗi bước `cur` có tối đa 32 phần tử.
- **Không gian:** $O(N \times 32)$ cho `HashSet`.

---

## 19. Maximum Product of Word Lengths (LeetCode 318)

**Đề bài chi tiết:**
Cho mảng chuỗi `words`. Trả về giá trị lớn nhất của `length(word[i]) * length(word[j])` sao cho hai chuỗi không chia sẻ bất kỳ ký tự chung nào. Nếu không có cặp nào như vậy, trả về 0.

**Phân tích thuật toán:**
Vì các chuỗi chỉ chứa chữ cái tiếng Anh in thường, ta có thể dùng một số nguyên 32 bit để làm mask biểu diễn sự xuất hiện của 26 ký tự. Bit thứ `k` bằng 1 nếu ký tự thứ `k` xuất hiện.
Hai chuỗi không có ký tự chung nếu `mask[i] & mask[j] == 0`. Ta tính mask cho mọi chuỗi rồi lồng 2 vòng lặp tìm giá trị lớn nhất.

**Mã nguồn Java:**
```java
public class Solution {
    public int maxProduct(String[] words) {
        int n = words.length;
        int[] masks = new int[n];
        int[] lengths = new int[n];
        
        for (int i = 0; i < n; i++) {
            int mask = 0;
            for (char c : words[i].toCharArray()) {
                mask |= 1 << (c - 'a');
            }
            masks[i] = mask;
            lengths[i] = words[i].length();
        }
        
        int maxVal = 0;
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if ((masks[i] & masks[j]) == 0) {
                    maxVal = Math.max(maxVal, lengths[i] * lengths[j]);
                }
            }
        }
        
        return maxVal;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(L + N^2)$ với $L$ là tổng độ dài các chuỗi, $N$ là số lượng chuỗi.
- **Không gian:** $O(N)$.

---

## 20. Decode XORed Permutation (LeetCode 1734)

**Đề bài chi tiết:**
Cho một mảng `encoded` có độ dài $n-1$, trong đó `encoded[i] = perm[i] XOR perm[i+1]`, với `perm` là một hoán vị của $n$ số nguyên dương đầu tiên ($n$ là số lẻ). Hãy khôi phục mảng `perm`.

**Phân tích thuật toán:**
Vì `perm` là hoán vị của $n$ số nguyên đầu tiên, ta có thể tính tổng XOR của toàn bộ mảng `perm` (gọi là `totalXor`), từ $1$ đến $n$.
Tiếp theo, ta tìm XOR của tất cả các phần tử trừ `perm[0]`. Bằng cách lấy XOR các phần tử cách nhau 2 bước trong mảng `encoded`: `encoded[1] ^ encoded[3] ^ ... ^ encoded[n-2]`, kết quả này chính là `perm[1] ^ perm[2] ^ perm[3] ^ ... ^ perm[n-1]` (gọi là `partXor`).
Khi đó, `perm[0] = totalXor ^ partXor`. Biết `perm[0]`, ta khôi phục toàn bộ `perm` bằng `perm[i] = perm[i-1] ^ encoded[i-1]`.

**Mã nguồn Java:**
```java
public class Solution {
    public int[] decode(int[] encoded) {
        int n = encoded.length + 1;
        int totalXor = 0;
        for (int i = 1; i <= n; i++) {
            totalXor ^= i;
        }
        
        int partXor = 0;
        // XOR tất cả các cặp ngoại trừ phần tử đầu tiên
        for (int i = 1; i < n - 1; i += 2) {
            partXor ^= encoded[i];
        }
        
        int[] perm = new int[n];
        perm[0] = totalXor ^ partXor;
        for (int i = 1; i < n; i++) {
            perm[i] = perm[i - 1] ^ encoded[i - 1];
        }
        
        return perm;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N)$.
- **Không gian:** $O(N)$ để trả về mảng kết quả.

---

## 21. Check If a String Contains All Binary Codes of Size K (LeetCode 1461)

**Đề bài chi tiết:**
Cho một chuỗi nhị phân `s` và một số nguyên `k`. Hãy trả về `true` nếu mọi mã nhị phân có độ dài `k` đều là chuỗi con của `s`. Ngược lại, trả về `false`.

**Phân tích thuật toán:**
Số lượng mã nhị phân có độ dài `k` là $2^k$. Do đó, ta chỉ cần lặp qua tất cả các chuỗi con có độ dài `k` trong `s` và đưa chúng vào một tập hợp (HashSet). Sau khi duyệt qua chuỗi `s`, nếu kích thước của tập hợp đúng bằng $2^k$, tức là mọi mã nhị phân đều xuất hiện.
Để tối ưu, thay vì cắt chuỗi (substring), ta có thể sử dụng rolling hash hoặc bit manipulation để tạo số nguyên tương ứng. Tuy nhiên, nếu $k \le 20$, hàm substring kết hợp Hash Set cũng đủ hiệu quả cho đa số test cases.
Sử dụng Bit manipulation (Rolling Hash): Mỗi chuỗi con độ dài `k` tương ứng với một số nguyên, dịch trái 1 bit, cộng thêm bit mới, và loại bỏ bit cao nhất bị thừa bằng một mask.

**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public boolean hasAllCodes(String s, int k) {
        if (s.length() < k + (1 << k) - 1) return false;
        
        Set<Integer> set = new HashSet<>();
        int hash = 0;
        int mask = (1 << k) - 1; // Mask để giữ lại đúng k bit
        
        // Khởi tạo hash ban đầu
        for (int i = 0; i < s.length(); i++) {
            hash = ((hash << 1) & mask) | (s.charAt(i) - '0');
            if (i >= k - 1) {
                set.add(hash);
                // Thoát sớm nếu đã đủ 2^k
                if (set.size() == (1 << k)) return true;
            }
        }
        
        return set.size() == (1 << k);
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N)$, với $N$ là độ dài của chuỗi `s`.
- **Không gian:** $O(2^k)$, kích thước của Hash Set chứa số nguyên.

---

## 22. Find Longest Awesome Substring (LeetCode 1542)

**Đề bài chi tiết:**
Cho một chuỗi chữ số `s`. Một chuỗi được gọi là "awesome" nếu có thể hoán vị các ký tự của nó để tạo thành một chuỗi đối xứng (palindrome). Tìm độ dài chuỗi con awesome dài nhất.

**Phân tích thuật toán:**
Một chuỗi có thể xếp thành palindrome nếu nó có nhiều nhất 1 ký tự xuất hiện với số lần lẻ. Chuỗi `s` chỉ chứa các chữ số từ 0-9, vậy trạng thái chẵn lẻ của 10 chữ số có thể biểu diễn bằng một bitmask 10 bit. (Bit thứ $i$ là 1 nếu chữ số $i$ xuất hiện số lần lẻ).
Ta tính prefix bitmask tại mỗi vị trí. Đoạn con từ $i$ đến $j$ là awesome nếu:
1. `mask[j] == mask[i-1]` (tất cả các chữ số đều xuất hiện chẵn lần).
2. `mask[j] ^ mask[i-1] == 1 << d` (chỉ duy nhất chữ số $d$ xuất hiện lẻ lần).
Sử dụng mảng/HashMap lưu trữ vị trí đầu tiên xuất hiện của mỗi bitmask để tính độ dài chuỗi dài nhất.

**Mã nguồn Java:**
```java
import java.util.Arrays;

public class Solution {
    public int longestAwesome(String s) {
        int[] firstPos = new int[1024]; // 2^10 = 1024 trạng thái mask
        Arrays.fill(firstPos, -1);
        firstPos[0] = 0; // Trạng thái chẵn toàn bộ trước ký tự đầu
        
        int mask = 0;
        int maxLen = 0;
        
        for (int i = 0; i < s.length(); i++) {
            mask ^= (1 << (s.charAt(i) - '0')); // Cập nhật trạng thái chẵn lẻ
            
            // Trường hợp toàn bộ ký tự chẵn lần
            if (firstPos[mask] != -1) {
                maxLen = Math.max(maxLen, i + 1 - firstPos[mask]);
            } else {
                firstPos[mask] = i + 1; // Chỉ ghi nhận lần đầu xuất hiện
            }
            
            // Trường hợp có đúng 1 ký tự lẻ lần
            for (int d = 0; d < 10; d++) {
                int expectedMask = mask ^ (1 << d);
                if (firstPos[expectedMask] != -1) {
                    maxLen = Math.max(maxLen, i + 1 - firstPos[expectedMask]);
                }
            }
        }
        
        return maxLen;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(10 \times N) = O(N)$.
- **Không gian:** $O(2^{10}) = O(1)$ vì số trạng thái bị giới hạn.

---

## 23. XOR Queries of a Subarray (LeetCode 1310)

**Đề bài chi tiết:**
Cho mảng số nguyên dương `arr` và một mảng truy vấn `queries` trong đó `queries[i] = [left_i, right_i]`. 
Với mỗi truy vấn, tính XOR của các phần tử từ `left_i` đến `right_i` (tức là `arr[left_i] XOR ... XOR arr[right_i]`). Hãy trả về một mảng chứa kết quả.

**Phân tích thuật toán:**
Tương tự bài Prefix Sum, ta có thể tạo mảng Prefix XOR. Đặt `prefix[i] = arr[0] ^ arr[1] ^ ... ^ arr[i-1]`.
Khi đó, phép XOR từ `left` đến `right` có thể được tính nhanh trong $O(1)$ bằng công thức: `prefix[right + 1] ^ prefix[left]`.
Phép toán này triệt tiêu tất cả các phần tử trước `left` vì $A \oplus A = 0$.

**Mã nguồn Java:**
```java
public class Solution {
    public int[] xorQueries(int[] arr, int[][] queries) {
        int n = arr.length;
        int[] prefix = new int[n + 1];
        
        // Tiền xử lý prefix XOR
        for (int i = 0; i < n; i++) {
            prefix[i + 1] = prefix[i] ^ arr[i];
        }
        
        int[] result = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            int left = queries[i][0];
            int right = queries[i][1];
            // Tính XOR đoạn [left, right]
            result[i] = prefix[right + 1] ^ prefix[left];
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N + Q)$, trong đó $N$ là kích thước mảng `arr` và $Q$ là số lượng truy vấn.
- **Không gian:** $O(N)$ cho mảng prefix.

---

## 24. Number of Steps to Reduce a Number in Binary Representation to One (LeetCode 1404)

**Đề bài chi tiết:**
Cho biểu diễn nhị phân của một số nguyên dưới dạng chuỗi `s`. Số thao tác cần thiết để biến số này về 1 được tính như sau:
- Nếu số hiện tại chẵn, chia nó cho 2.
- Nếu số hiện tại lẻ, cộng thêm 1.
Hãy trả về số bước thực hiện.

**Phân tích thuật toán:**
Do số nguyên rất lớn (dạng chuỗi lên tới $500$ ký tự), ta không thể chuyển chuỗi thành số thực sự. Thay vào đó, ta duyệt từ phải sang trái (từ bit thấp nhất lên cao nhất).
- Chia 2 tương đương với dịch phải 1 bit (xóa ký tự cuối).
- Cộng 1 sẽ ảnh hưởng tới chuỗi bit tùy thuộc vào nhớ (carry).
Ta dùng biến `carry`. Xét bit `c = s.charAt(i) - '0' + carry`. 
Nếu `c == 1` (số lẻ), ta tốn 2 bước: 1 bước cộng (biến c thành 2, tức là bit 0 nhớ 1), 1 bước chia 2 (xóa số 0). Và `carry` trở thành 1.
Nếu `c == 0` (số chẵn), ta chỉ tốn 1 bước: chia 2. `carry` giữ nguyên 0.
Nếu `c == 2` (số chẵn do có nhớ), ta tốn 1 bước chia 2, `carry` giữ nguyên 1.

**Mã nguồn Java:**
```java
public class Solution {
    public int numSteps(String s) {
        int steps = 0;
        int carry = 0;
        
        // Không duyệt tới phần tử đầu tiên (bit cao nhất)
        for (int i = s.length() - 1; i > 0; i--) {
            int bit = s.charAt(i) - '0' + carry;
            if (bit == 1) { // Lẻ -> 1 bước cộng + 1 bước chia
                steps += 2;
                carry = 1;
            } else { // Chẵn (bit 0 hoặc bit 2 từ nhớ) -> 1 bước chia
                steps += 1;
                // Nếu bit=2, carry duy trì là 1; nếu bit=0, carry duy trì 0.
                if (bit == 2) carry = 1;
            }
        }
        
        // Ở ký tự đầu tiên, nếu có nhớ, ta sẽ có bit 2, cần 1 bước cộng thêm
        return steps + carry;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N)$ với $N$ là độ dài của chuỗi.
- **Không gian:** $O(1)$.

---

## 25. Sort Integers by The Number of 1 Bits (LeetCode 1356)

**Đề bài chi tiết:**
Cho mảng số nguyên `arr`. Hãy sắp xếp các số này theo số lượng bit `1` trong biểu diễn nhị phân của chúng theo thứ tự tăng dần. Nếu có hai hoặc nhiều số có cùng số bit `1`, hãy sắp xếp chúng theo giá trị thực của chúng theo thứ tự tăng dần.

**Phân tích thuật toán:**
Viết một hàm hoặc bộ so sánh (Comparator) tùy chỉnh. Để đếm số lượng bit 1 của một số $X$, ta có thể dùng `Integer.bitCount(X)` trong Java hoặc thuật toán Kernighan `n & (n-1)`. Do Java không hỗ trợ trực tiếp hàm sắp xếp nguyên thủy `int[]` với custom Comparator, ta có thể chuyển dữ liệu sang `Integer[]` hoặc gộp số bit vào giá trị gốc rồi sắp xếp.
Một thủ thuật tối ưu mà không cần `Integer[]`: Gán `arr[i] += bitCount(arr[i]) * 100000`. Sắp xếp mảng, sau đó khôi phục `arr[i] %= 100000`.

**Mã nguồn Java:**
```java
import java.util.Arrays;

public class Solution {
    public int[] sortByBits(int[] arr) {
        // Áp dụng mẹo gộp: bitCount * 100000 + giá trị
        // (Do giới hạn giá trị arr[i] <= 10^4 nên x100000 hoàn toàn an toàn)
        for (int i = 0; i < arr.length; i++) {
            arr[i] += Integer.bitCount(arr[i]) * 100000;
        }
        
        Arrays.sort(arr);
        
        // Khôi phục mảng gốc
        for (int i = 0; i < arr.length; i++) {
            arr[i] %= 100000;
        }
        
        return arr;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N \log N)$ để sắp xếp. Tính số bit mất $O(N)$.
- **Không gian:** $O(1)$ (nếu bỏ qua bộ nhớ callstack của Arrays.sort).

---

## 26. Convert a Number to Hexadecimal (LeetCode 405)

**Đề bài chi tiết:**
Cho một số nguyên, viết một thuật toán để chuyển đổi nó thành chuỗi biểu diễn dưới hệ thập lục phân (hexadecimal). Cần xử lý cẩn thận với số âm bằng bù 2 (two's complement) và không được sử dụng các hàm chuyển đổi có sẵn trong thư viện.

**Phân tích thuật toán:**
Một số nguyên 32 bit có thể được chia làm 8 nhóm, mỗi nhóm 4 bit đại diện cho một ký tự Hex.
Ta sử dụng bitmask `15` (`0b1111`) để lấy 4 bit thấp nhất. Sau khi ánh xạ ra ký tự (dùng chuỗi `"0123456789abcdef"`), ta dịch chuyển số sang phải 4 bit theo kiểu không dấu (`>>> 4`). Lặp lại quá trình tới khi số nguyên bằng 0. Đảo ngược kết quả thu được.

**Mã nguồn Java:**
```java
public class Solution {
    public String toHex(int num) {
        if (num == 0) return "0";
        
        char[] hexChars = "0123456789abcdef".toCharArray();
        StringBuilder sb = new StringBuilder();
        
        // Dịch phải (unsigned) 4 bit mỗi lần, xử lý tối đa 8 lần cho 32-bit
        while (num != 0) {
            int val = num & 15; // Lấy 4 bit cuối
            sb.append(hexChars[val]);
            num >>>= 4; // Dịch không dấu
        }
        
        // Đảo ngược chuỗi vì ta lấy bit từ thấp lên cao
        return sb.reverse().toString();
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(1)$ (tối đa 8 vòng lặp).
- **Không gian:** $O(1)$.

---

## 27. Number of Valid Words for Each Puzzle (LeetCode 1178)

**Đề bài chi tiết:**
Cho một danh sách `words` và một danh sách `puzzles`. Một từ được xem là "hợp lệ" với một puzzle nếu:
- Từ đó chứa ký tự đầu tiên của puzzle.
- Mọi ký tự của từ đó phải có mặt trong puzzle.
Tính số lượng từ hợp lệ tương ứng với từng puzzle. Các puzzle có độ dài đúng 7 ký tự và không chứa ký tự trùng lặp.

**Phân tích thuật toán:**
Vì mỗi puzzle đều rất ngắn và bảng chữ cái tiếng Anh in thường chỉ có 26 ký tự, ta biểu diễn tập hợp ký tự của mỗi từ bằng một bitmask. 
Tiếp theo, ta nhóm các từ có cùng bitmask và đếm số lượng.
Với mỗi puzzle, ta chỉ xét các "tập con" của puzzle (tức là sinh mọi submask). Nếu submask nào chứa ký tự đầu tiên của puzzle, ta cộng số lượng từ khớp với submask đó. Kỹ thuật sinh submask là `submask = (submask - 1) & mask`.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class Solution {
    public List<Integer> findNumOfValidWords(String[] words, String[] puzzles) {
        Map<Integer, Integer> wordCount = new HashMap<>();
        // Ánh xạ words thành bitmask và đếm
        for (String w : words) {
            int mask = 0;
            for (char c : w.toCharArray()) mask |= (1 << (c - 'a'));
            wordCount.put(mask, wordCount.getOrDefault(mask, 0) + 1);
        }
        
        List<Integer> ans = new ArrayList<>();
        for (String p : puzzles) {
            int mask = 0;
            for (char c : p.toCharArray()) mask |= (1 << (c - 'a'));
            
            int firstCharBit = 1 << (p.charAt(0) - 'a');
            int count = 0;
            
            // Duyệt qua tất cả các submask của mask puzzle
            int submask = mask;
            while (submask > 0) {
                // Hợp lệ nếu chứa first char
                if ((submask & firstCharBit) != 0 && wordCount.containsKey(submask)) {
                    count += wordCount.get(submask);
                }
                // Chuyển sang submask tiếp theo
                submask = (submask - 1) & mask;
            }
            ans.add(count);
        }
        return ans;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N \cdot L_w + M \cdot 2^{L_p})$ với $N$ là số words, $L_w$ độ dài word trung bình. $M$ là số puzzles, $L_p = 7$.
- **Không gian:** $O(N)$ lưu số lượng word mask.

---

## 28. Find the K-or of an Array (LeetCode 2917)

**Đề bài chi tiết:**
Cho một mảng `nums` và một số nguyên `k`. K-or của mảng là một số nguyên mà bit thứ $i$ được thiết lập là 1 nếu và chỉ nếu có ít nhất `k` phần tử trong mảng có bit thứ $i$ là 1. Tính giá trị K-or đó.

**Phân tích thuật toán:**
Đề bài cho biết định nghĩa của K-or. Chúng bản chất ta chỉ cần lặp qua mọi bit từ 0 đến 31. Đối với mỗi vị trí bit $i$, đếm xem có bao nhiêu phần tử trong mảng có bit đó bằng 1. Nếu đếm được $\ge k$, ta thiết lập bit thứ $i$ trong biến kết quả (sử dụng toán tử bitwise OR).

**Mã nguồn Java:**
```java
public class Solution {
    public int findKOr(int[] nums, int k) {
        int kOr = 0;
        
        for (int i = 0; i < 32; i++) {
            int count = 0;
            for (int num : nums) {
                if (((num >> i) & 1) == 1) {
                    count++;
                }
            }
            if (count >= k) {
                kOr |= (1 << i);
            }
        }
        
        return kOr;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N \times 32) \approx O(N)$.
- **Không gian:** $O(1)$.

---

## 29. Minimum Flips to Make a OR b Equal to c (LeetCode 1318)

**Đề bài chi tiết:**
Cho 3 số dương `a`, `b`, `c`. Trả về số lần lật (flip) bit tối thiểu ở `a` và `b` để thỏa mãn phép toán `a OR b == c`. Phép lật bit ở 1 vị trí có thể chuyển 0 thành 1 và ngược lại.

**Phân tích thuật toán:**
So sánh từng bit riêng rẽ từ vị trí 0 đến 31 của cả `a`, `b`, và `c`.
- Nếu bit của `c` là 1: Ít nhất 1 trong 2 số `a` hoặc `b` phải có bit tương ứng bằng 1. Nếu cả hai đều bằng 0, ta cần 1 phép lật bit (chọn `a` hoặc `b` để lật thành 1).
- Nếu bit của `c` là 0: Cả hai số `a` và `b` đều phải có bit bằng 0. Nếu `a` có bit 1, tốn 1 flip. Nếu `b` có bit 1, tốn thêm 1 flip nữa.

**Mã nguồn Java:**
```java
public class Solution {
    public int minFlips(int a, int b, int c) {
        int flips = 0;
        
        for (int i = 0; i < 32; i++) {
            int bitA = (a >> i) & 1;
            int bitB = (b >> i) & 1;
            int bitC = (c >> i) & 1;
            
            if (bitC == 1) {
                if (bitA == 0 && bitB == 0) {
                    flips += 1;
                }
            } else { // bitC == 0
                flips += bitA + bitB;
            }
        }
        
        return flips;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(32) = O(1)$.
- **Không gian:** $O(1)$.

---

## 30. Maximize Score After N Operations (LeetCode 1799)

**Đề bài chi tiết:**
Bạn nhận được mảng `nums` gồm $2N$ số nguyên dương. Bạn phải thực hiện $N$ thao tác. Ở thao tác thứ $i$ ($1 \le i \le N$), bạn chọn 2 phần tử `x` và `y` chưa được chọn từ mảng, rồi nhận được điểm số $i \times \gcd(x, y)$. Sau đó xóa chúng. Tính tổng số điểm cao nhất có thể đạt được.

**Phân tích thuật toán:**
Vì $2N \le 14$, ta hoàn toàn có thể sử dụng Bitmask DP (Quy hoạch động trạng thái). Bitmask biểu diễn những phần tử đã được sử dụng. Nếu bit $k$ là 1, tức là phần tử $k$ vẫn còn trong mảng.
Ta dùng hàm đệ quy có nhớ (Memoization). Mỗi trạng thái `mask`, ta tính toán vòng hiện tại `ops = (trạng thái mask chứa 1)/2 + 1` (ví dụ ban đầu chưa chọn phần tử nào có nghĩa là mask = $2^{2N}-1$, số bit 1 là chẵn).
Thực tế, tiện nhất là lưu các số ĐÃ ĐƯỢC CHỌN (0 là chưa chọn, 1 là đã chọn). `ops = số bit 1 / 2 + 1`. Lặp lấy 2 bit đang là 0 trong mask chuyển thành 1, tính GCD và cộng vào đệ quy tiếp.

**Mã nguồn Java:**
```java
public class Solution {
    // Hàm tìm ước chung lớn nhất
    private int gcd(int a, int b) {
        return b == 0 ? a : gcd(b, a % b);
    }
    
    public int maxScore(int[] nums) {
        int m = nums.length;
        Integer[] memo = new Integer[1 << m];
        return dfs(nums, 0, 1, memo);
    }
    
    private int dfs(int[] nums, int mask, int op, Integer[] memo) {
        int m = nums.length;
        if (mask == (1 << m) - 1) { // Mọi phần tử đều đã được chọn
            return 0;
        }
        
        if (memo[mask] != null) {
            return memo[mask];
        }
        
        int maxScore = 0;
        
        // Duyệt tìm 2 phần tử chưa được chọn
        for (int i = 0; i < m; i++) {
            if ((mask & (1 << i)) != 0) continue;
            for (int j = i + 1; j < m; j++) {
                if ((mask & (1 << j)) != 0) continue;
                
                int newMask = mask | (1 << i) | (1 << j);
                int score = op * gcd(nums[i], nums[j]) + dfs(nums, newMask, op + 1, memo);
                maxScore = Math.max(maxScore, score);
            }
        }
        
        return memo[mask] = maxScore;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** Số lượng trạng thái $O(2^{2N})$, với mỗi trạng thái ta duyệt qua các cặp $O(N^2)$. Tổng thời gian $O(N^2 2^{2N})$.
- **Không gian:** $O(2^{2N})$ cho mảng nhớ (memo).
