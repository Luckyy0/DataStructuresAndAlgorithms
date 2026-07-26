# 05 - Prefix Sum and Hashing Problems

Tài liệu này cung cấp danh sách 30 bài tập về kĩ thuật Prefix Sum kết hợp Hashing. 10 bài đầu tiên đi kèm phân tích chi tiết và code Java, 20 bài sau là các định hướng tóm tắt nâng cao.

## 1. Subarray Sum Equals K
**Đề bài chi tiết:** Cho một mảng số nguyên `nums` và một số nguyên `k`, trả về số lượng các mảng con liên tục có tổng bằng `k`.
**Phân tích thuật toán:** Sử dụng Hash Map để lưu tần suất của các Prefix Sum. Với mỗi `num` tại chỉ số `i`, tính `sum += num`. Số lượng mảng con kết thúc tại `i` có tổng `k` chính là số lần `sum - k` đã xuất hiện trong Hash Map. Nhớ khởi tạo `map.put(0, 1)`.
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class Solution {
    public int subarraySum(int[] nums, int k) {
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0, 1);
        int sum = 0;
        int count = 0;
        
        for (int num : nums) {
            sum += num;
            if (map.containsKey(sum - k)) {
                count += map.get(sum - k);
            }
            map.put(sum, map.getOrDefault(sum, 0) + 1);
        }
        return count;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(N).

---

## 2. Contiguous Array
**Đề bài chi tiết:** Cho một mảng nhị phân `nums`, tìm chiều dài lớn nhất của một mảng con liên tục chứa số lượng 0 và 1 bằng nhau.
**Phân tích thuật toán:** Coi tất cả số `0` thành `-1`. Bài toán trở thành tìm mảng con dài nhất có tổng bằng `0`. Dùng Hash Map lưu `(prefix_sum, index)`. Khởi tạo `map.put(0, -1)`. Chỉ lưu chỉ số đầu tiên xuất hiện để tối đa hóa chiều dài.
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class Solution {
    public int findMaxLength(int[] nums) {
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0, -1);
        int maxLength = 0;
        int sum = 0;
        
        for (int i = 0; i < nums.length; i++) {
            sum += (nums[i] == 0) ? -1 : 1;
            
            if (map.containsKey(sum)) {
                maxLength = Math.max(maxLength, i - map.get(sum));
            } else {
                map.put(sum, i);
            }
        }
        return maxLength;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(N).

---

## 3. Subarray Sums Divisible by K
**Đề bài chi tiết:** Cho một mảng số nguyên `nums` và một số nguyên `k`, trả về số lượng mảng con liên tục có tổng chia hết cho `k`.
**Phân tích thuật toán:** Thay vì lưu prefix sum, ta lưu phần dư `prefix_sum % k`. Nếu tổng hai prefix sum có cùng phần dư, tổng mảng con giữa chúng chia hết cho `k`. Xử lý số âm cẩn thận: `(sum % k + k) % k`.
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class Solution {
    public int subarraysDivByK(int[] nums, int k) {
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0, 1);
        int count = 0;
        int sum = 0;
        
        for (int num : nums) {
            sum += num;
            int rem = (sum % k + k) % k;
            
            if (map.containsKey(rem)) {
                count += map.get(rem);
            }
            map.put(rem, map.getOrDefault(rem, 0) + 1);
        }
        return count;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(min(N, K)).

---

## 4. Maximum Size Subarray Sum Equals k
**Đề bài chi tiết:** Cho mảng `nums` và số `k`, tìm độ dài lớn nhất của mảng con có tổng đúng bằng `k`. Nếu không tồn tại, trả về 0.
**Phân tích thuật toán:** Tương tự bài 1 nhưng lưu `(prefix_sum, index)` thay vì `count`. Nếu `sum - k` tồn tại trong Map, cập nhật max length = `i - map.get(sum - k)`. Chỉ lưu `sum` mới nếu chưa có trong Map để giữ khoảng cách xa nhất.
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class Solution {
    public int maxSubArrayLen(int[] nums, int k) {
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0, -1);
        int sum = 0;
        int maxLen = 0;
        
        for (int i = 0; i < nums.length; i++) {
            sum += nums[i];
            
            if (map.containsKey(sum - k)) {
                maxLen = Math.max(maxLen, i - map.get(sum - k));
            }
            
            if (!map.containsKey(sum)) {
                map.put(sum, i);
            }
        }
        return maxLen;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(N).

---

## 5. Path Sum III (Tree + Prefix Sum)
**Đề bài chi tiết:** Cho root của một cây nhị phân và số nguyên `targetSum`, trả về số lượng các đường đi đi xuống (từ nút cha đến nút con) có tổng bằng `targetSum`.
**Phân tích thuật toán:** Duyệt cây DFS bằng đệ quy. Pass Hash Map lưu tiền sử các Prefix Sum dọc theo nhánh từ root xuống. Khi lùi (backtrack), ta phải giảm số lượng prefix sum ở node hiện tại đi 1.
**Mã nguồn Java:**
```java
import java.util.HashMap;

class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int x) { val = x; }
}

public class Solution {
    public int pathSum(TreeNode root, int targetSum) {
        HashMap<Long, Integer> prefixMap = new HashMap<>();
        prefixMap.put(0L, 1);
        return dfs(root, 0L, targetSum, prefixMap);
    }
    
    private int dfs(TreeNode node, long currSum, int target, HashMap<Long, Integer> map) {
        if (node == null) return 0;
        
        currSum += node.val;
        int count = map.getOrDefault(currSum - target, 0);
        
        map.put(currSum, map.getOrDefault(currSum, 0) + 1);
        
        count += dfs(node.left, currSum, target, map);
        count += dfs(node.right, currSum, target, map);
        
        map.put(currSum, map.get(currSum) - 1);
        return count;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(H) (H là chiều cao cây).

---

## 6. Continuous Subarray Sum
**Đề bài chi tiết:** Cho mảng `nums` và số `k`, trả về `true` nếu mảng có chứa mảng con liên tục có kích thước ít nhất là 2 và tổng chia hết cho `k`.
**Phân tích thuật toán:** Lưu lại phần dư của tổng tiền tố vào Map, key là phần dư, value là index. Vì cần chiều dài >= 2, kiểm tra `i - map.get(rem) > 1`.
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class Solution {
    public boolean checkSubarraySum(int[] nums, int k) {
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0, -1);
        int sum = 0;
        
        for (int i = 0; i < nums.length; i++) {
            sum += nums[i];
            int rem = sum % k;
            
            if (map.containsKey(rem)) {
                if (i - map.get(rem) > 1) {
                    return true;
                }
            } else {
                map.put(rem, i);
            }
        }
        return false;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(min(N, K)).

---

## 7. Make Sum Divisible by P
**Đề bài chi tiết:** Cần loại bỏ mảng con nhỏ nhất sao cho tổng các phần tử còn lại chia hết cho `p`.
**Phân tích thuật toán:** Tính `target = sum(nums) % p`. Nếu `target == 0`, trả về 0. Cần tìm mảng con ngắn nhất có tổng chia `p` dư `target`. Dùng Map lưu `(prefix_sum % p, index)`. Khóa tra cứu: `(currSum - target + p) % p`.
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class Solution {
    public int minSubarray(int[] nums, int p) {
        long total = 0;
        for (int num : nums) total += num;
        int target = (int)(total % p);
        if (target == 0) return 0;
        
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0, -1);
        int minLen = nums.length;
        int sum = 0;
        
        for (int i = 0; i < nums.length; i++) {
            sum = (sum + nums[i]) % p;
            int needed = (sum - target + p) % p;
            
            if (map.containsKey(needed)) {
                minLen = Math.min(minLen, i - map.get(needed));
            }
            map.put(sum, i);
        }
        return minLen == nums.length ? -1 : minLen;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(min(N, p)).

---

## 8. Maximum Points You Can Obtain from Cards
**Đề bài chi tiết:** Lấy `k` thẻ từ hai đầu của mảng `cardPoints` sao cho tổng điểm tối đa.
**Phân tích thuật toán:** Lấy `k` thẻ từ 2 đầu đồng nghĩa với để lại một mảng con liên tục kích thước `n - k` có tổng nhỏ nhất. Ta dùng Prefix Sum để tìm mảng con `n - k` có tổng nhỏ nhất hoặc dùng Sliding Window. Ở đây Sliding Window dễ hơn do kích thước cố định. Dưới đây áp dụng ý tưởng tổng cố định.
**Mã nguồn Java:**
```java
public class Solution {
    public int maxScore(int[] cardPoints, int k) {
        int n = cardPoints.length;
        int windowSum = 0;
        for (int i = 0; i < n - k; i++) {
            windowSum += cardPoints[i];
        }
        
        int minSubarraySum = windowSum;
        int totalSum = windowSum;
        
        for (int i = n - k; i < n; i++) {
            windowSum = windowSum - cardPoints[i - (n - k)] + cardPoints[i];
            minSubarraySum = Math.min(minSubarraySum, windowSum);
            totalSum += cardPoints[i];
        }
        
        return totalSum - minSubarraySum;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(1).

---

## 9. Count Number of Nice Subarrays
**Đề bài chi tiết:** Cho mảng `nums` và số nguyên `k`, một mảng con được gọi là "đẹp" nếu nó chứa chính xác `k` số lẻ.
**Phân tích thuật toán:** Chuyển số lẻ thành `1`, số chẵn thành `0`. Bài toán biến thành đếm số lượng mảng con có tổng bằng `k` (giống y hệt bài 1).
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class Solution {
    public int numberOfSubarrays(int[] nums, int k) {
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0, 1);
        int sum = 0, count = 0;
        
        for (int num : nums) {
            sum += (num % 2 != 0) ? 1 : 0;
            if (map.containsKey(sum - k)) {
                count += map.get(sum - k);
            }
            map.put(sum, map.getOrDefault(sum, 0) + 1);
        }
        return count;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(N).

---

## 10. Find Longest Awesome Substring
**Đề bài chi tiết:** Chuỗi "awesome" là chuỗi có thể đổi chỗ các kí tự để tạo thành Palindrome. Tìm chiều dài chuỗi con awesome dài nhất.
**Phân tích thuật toán:** Dùng State Hashing (Bitmask). Chữ số từ '0'-'9'. Một chuỗi tạo thành Palindrome nếu có tối đa 1 kí tự xuất hiện số lần lẻ. Dùng 1 integer làm mask (10 bit) lưu chẵn lẻ của tần suất từng chữ số. Mảng con hợp lệ nếu XOR mask bằng 0 hoặc có dạng lũy thừa của 2 (chỉ 1 bit 1). Lưu vị trí xuất hiện đầu tiên của mỗi trạng thái.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class Solution {
    public int longestAwesome(String s) {
        int[] map = new int[1024];
        Arrays.fill(map, -1);
        map[0] = 0; // Trạng thái ban đầu ở index 0 (kí hiệu ảo) -> để map 1-based logic
        // Ta dùng cách chuẩn map.put(0, -1) cho index.
        Arrays.fill(map, -2);
        map[0] = -1;
        
        int mask = 0, maxLen = 0;
        for (int i = 0; i < s.length(); i++) {
            mask ^= (1 << (s.charAt(i) - '0'));
            
            if (map[mask] != -2) {
                maxLen = Math.max(maxLen, i - map[mask]);
            } else {
                map[mask] = i;
            }
            
            for (int j = 0; j < 10; j++) {
                int flippedMask = mask ^ (1 << j);
                if (map[flippedMask] != -2) {
                    maxLen = Math.max(maxLen, i - map[flippedMask]);
                }
            }
        }
        return maxLen;
    }
}
```
**Độ phức tạp:** Thời gian O(10 * N), Không gian O(1024) = O(1).

---

## 11. Binary Subarrays With Sum
**Đề bài chi tiết:** Cho mảng nhị phân `nums` và một số nguyên `goal`, trả về số lượng các mảng con liên tục có tổng bằng `goal`.
**Phân tích thuật toán:** Tương tự như bài toán đếm mảng con có tổng bằng `k` (Bài 1). Vì các phần tử chỉ là `0` hoặc `1`, tổng tiền tố lớn nhất có thể đạt là chiều dài của mảng, do đó ta có thể sử dụng một mảng tĩnh (array) để thay thế Hash Map nhằm tối ưu hóa thời gian chạy và không gian bộ nhớ. Đếm tần suất xuất hiện của mỗi tổng tiền tố và cộng dồn các kết quả `sum - goal`.
**Mã nguồn Java:**
```java
public class Solution {
    public int numSubarraysWithSum(int[] nums, int goal) {
        int[] count = new int[nums.length + 1];
        count[0] = 1;
        int sum = 0, result = 0;
        
        for (int num : nums) {
            sum += num;
            if (sum >= goal) {
                result += count[sum - goal];
            }
            count[sum]++;
        }
        return result;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(N).

---

## 12. Longest Well-Performing Interval
**Đề bài chi tiết:** Cho mảng `hours` biểu diễn số giờ làm việc mỗi ngày. Một ngày được gọi là "năng suất" nếu số giờ làm việc `> 8`. Một khoảng thời gian "hiệu quả" là mảng con liên tục có số ngày năng suất nghiêm ngặt lớn hơn số ngày không năng suất. Trả về chiều dài lớn nhất của một khoảng thời gian hiệu quả.
**Phân tích thuật toán:** Chuyển đổi các phần tử `> 8` thành `1` và `<= 8` thành `-1`. Bài toán trở thành tìm mảng con liên tục dài nhất có tổng `> 0`. Sử dụng Hash Map lưu trữ vị trí đầu tiên xuất hiện của mỗi tổng tiền tố. Khi duyệt, nếu tổng tiền tố `sum > 0`, độ dài từ đầu đến hiện tại là hợp lệ. Nếu `sum <= 0`, ta tìm xem `sum - 1` có trong Hash Map hay không để tạo ra một khoảng có tổng bằng `1` (tức là `> 0`).
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class Solution {
    public int longestWPI(int[] hours) {
        HashMap<Integer, Integer> map = new HashMap<>();
        int maxLen = 0, sum = 0;
        
        for (int i = 0; i < hours.length; i++) {
            sum += hours[i] > 8 ? 1 : -1;
            
            if (sum > 0) {
                maxLen = i + 1;
            } else {
                if (!map.containsKey(sum)) {
                    map.put(sum, i);
                }
                if (map.containsKey(sum - 1)) {
                    maxLen = Math.max(maxLen, i - map.get(sum - 1));
                }
            }
        }
        return maxLen;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(N).

---

## 13. Find the Longest Substring Containing Vowels in Even Counts
**Đề bài chi tiết:** Cho chuỗi `s`, trả về độ dài lớn nhất của một chuỗi con liên tục sao cho mỗi nguyên âm ('a', 'e', 'i', 'o', 'u') xuất hiện với số lần chẵn.
**Phân tích thuật toán:** Sử dụng bitmask (5 bit) để lưu trạng thái chẵn lẻ của 5 nguyên âm. Mỗi khi gặp một nguyên âm, ta lật bit tương ứng (dùng phép XOR `^`). Nếu một trạng thái bitmask đã từng xuất hiện ở vị trí `j` trước đó và lặp lại ở vị trí `i`, phần chuỗi con giữa `j` và `i` sẽ có số lượng nguyên âm chẵn (do XOR 2 lần cùng 1 bit sẽ triệt tiêu). Lưu vị trí xuất hiện đầu tiên của mỗi bitmask.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class Solution {
    public int findTheLongestSubstring(String s) {
        int[] map = new int[32];
        Arrays.fill(map, -2);
        map[0] = -1;
        
        int mask = 0, maxLen = 0;
        String vowels = "aeiou";
        
        for (int i = 0; i < s.length(); i++) {
            int idx = vowels.indexOf(s.charAt(i));
            if (idx != -1) {
                mask ^= (1 << idx);
            }
            
            if (map[mask] != -2) {
                maxLen = Math.max(maxLen, i - map[mask]);
            } else {
                map[mask] = i;
            }
        }
        return maxLen;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(32) = O(1).

---

## 14. Vowels of All Substrings
**Đề bài chi tiết:** Cho chuỗi `word`, tính tổng số lượng nguyên âm xuất hiện trong tất cả các chuỗi con liên tục của `word`.
**Phân tích thuật toán:** Bài toán này không dùng Hash Map kết hợp Prefix Sum trực tiếp mà dựa vào toán học để tính sự đóng góp (contribution) của mỗi nguyên âm. Một nguyên âm tại vị trí `i` sẽ xuất hiện trong tất cả các chuỗi con bắt đầu từ hoặc trước `i` (có `i + 1` cách chọn điểm đầu) và kết thúc tại hoặc sau `i` (có `n - i` cách chọn điểm cuối). Tổng số chuỗi con chứa kí tự này là `(i + 1) * (n - i)`.
**Mã nguồn Java:**
```java
public class Solution {
    public long countVowels(String word) {
        long totalVowels = 0;
        int n = word.length();
        
        for (int i = 0; i < n; i++) {
            char c = word.charAt(i);
            if (c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u') {
                totalVowels += (long) (i + 1) * (n - i);
            }
        }
        return totalVowels;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(1).

---

## 15. Number of Submatrices That Sum to Target
**Đề bài chi tiết:** Cho một ma trận `matrix` và một số nguyên `target`, trả về số lượng ma trận con có tổng bằng `target`.
**Phân tích thuật toán:** Bài toán mở rộng của Prefix Sum 1D (bài 1). Ta cố định hai cột `c1` và `c2` (bằng 2 vòng lặp). Với mỗi cặp cột cố định, ta gộp tổng các phần tử trên từng hàng từ `c1` đến `c2` thành một mảng 1D, biến bài toán thành tìm số lượng mảng con 1D có tổng bằng `target` (có thể dùng Hash Map như bài 1).
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class Solution {
    public int numSubmatrixSumTarget(int[][] matrix, int target) {
        int r = matrix.length, c = matrix[0].length;
        int count = 0;
        
        for (int i = 0; i < r; i++) {
            for (int j = 1; j < c; j++) {
                matrix[i][j] += matrix[i][j - 1];
            }
        }
        
        for (int c1 = 0; c1 < c; c1++) {
            for (int c2 = c1; c2 < c; c2++) {
                HashMap<Integer, Integer> map = new HashMap<>();
                map.put(0, 1);
                int sum = 0;
                
                for (int i = 0; i < r; i++) {
                    int val = matrix[i][c2] - (c1 > 0 ? matrix[i][c1 - 1] : 0);
                    sum += val;
                    if (map.containsKey(sum - target)) {
                        count += map.get(sum - target);
                    }
                    map.put(sum, map.getOrDefault(sum, 0) + 1);
                }
            }
        }
        return count;
    }
}
```
**Độ phức tạp:** Thời gian O(R * C^2), Không gian O(R).

---

## 16. Maximum Size Submatrix That Sums to Zero
**Đề bài chi tiết:** Tìm kích thước (số lượng phần tử) lớn nhất của ma trận con có tổng bằng 0 trong một ma trận 2D. (Kích thước = số dòng x số cột).
**Phân tích thuật toán:** Tương tự bài 15, ta nén ma trận 2D thành 1D bằng cách cố định hai cột. Sau khi nén, ta tìm mảng con 1D dài nhất có tổng bằng 0 bằng Hash Map (giống ý tưởng bài 4, lưu `(prefix_sum, index)` thay vì `count`). Kích thước ma trận con lúc này sẽ là `(i - map.get(sum)) * (c2 - c1 + 1)`.
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class Solution {
    public int maxSubmatrix(int[][] matrix) {
        if (matrix == null || matrix.length == 0) return 0;
        int r = matrix.length, c = matrix[0].length;
        int maxSize = 0;
        
        for (int i = 0; i < r; i++) {
            for (int j = 1; j < c; j++) {
                matrix[i][j] += matrix[i][j - 1];
            }
        }
        
        for (int c1 = 0; c1 < c; c1++) {
            for (int c2 = c1; c2 < c; c2++) {
                HashMap<Integer, Integer> map = new HashMap<>();
                map.put(0, -1);
                int sum = 0;
                
                for (int i = 0; i < r; i++) {
                    int val = matrix[i][c2] - (c1 > 0 ? matrix[i][c1 - 1] : 0);
                    sum += val;
                    
                    if (map.containsKey(sum)) {
                        int currentRows = i - map.get(sum);
                        int currentCols = c2 - c1 + 1;
                        maxSize = Math.max(maxSize, currentRows * currentCols);
                    } else {
                        map.put(sum, i);
                    }
                }
            }
        }
        return maxSize;
    }
}
```
**Độ phức tạp:** Thời gian O(R * C^2), Không gian O(R).

---

## 17. Max Sum of Rectangle No Larger Than K
**Đề bài chi tiết:** Cho một ma trận và một số nguyên `k`, tìm tổng lớn nhất của ma trận con không vượt quá `k`.
**Phân tích thuật toán:** Vẫn áp dụng nén ma trận 2D thành mảng 1D như bài 15. Tuy nhiên, thay vì sử dụng Hash Map để tìm tổng đúng bằng `target`, ta cần tìm Prefix Sum nhỏ nhất trước đó lớn hơn hoặc bằng `sum - k` để làm cho `sum - prefix_sum <= k`. Để làm việc này, cấu trúc dữ liệu phù hợp là `TreeSet` (cung cấp phương thức `ceiling()` với chi phí O(log N)).
**Mã nguồn Java:**
```java
import java.util.TreeSet;

public class Solution {
    public int maxSumSubmatrix(int[][] matrix, int k) {
        int r = matrix.length, c = matrix[0].length;
        int maxSum = Integer.MIN_VALUE;
        
        for (int left = 0; left < c; left++) {
            int[] temp = new int[r];
            for (int right = left; right < c; right++) {
                for (int i = 0; i < r; i++) {
                    temp[i] += matrix[i][right];
                }
                
                TreeSet<Integer> set = new TreeSet<>();
                set.add(0);
                int currSum = 0;
                
                for (int val : temp) {
                    currSum += val;
                    Integer target = set.ceiling(currSum - k);
                    if (target != null) {
                        maxSum = Math.max(maxSum, currSum - target);
                    }
                    set.add(currSum);
                }
            }
        }
        return maxSum;
    }
}
```
**Độ phức tạp:** Thời gian O(C^2 * R log R), Không gian O(R).

---

## 18. Product of Array Except Self
**Đề bài chi tiết:** Cho mảng số nguyên `nums`, trả về mảng `answer` sao cho `answer[i]` bằng tích của tất cả các phần tử trong `nums` ngoại trừ `nums[i]`. Bạn phải viết thuật toán O(N) và không sử dụng phép chia.
**Phân tích thuật toán:** Bài này là một biến thể của kĩ thuật Prefix. Thay vì tính Prefix Sum, ta tính Prefix Product (Tích tiền tố). Kết quả tại `i` chính là tích của tất cả các phần tử bên trái nhân với tích tất cả các phần tử bên phải. Ta có thể duyệt mảng hai lần (từ trái qua phải và từ phải qua trái) để tính và nhân gộp tích vào kết quả mà không cần thêm mảng phụ trợ.
**Mã nguồn Java:**
```java
public class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] result = new int[n];
        
        result[0] = 1;
        for (int i = 1; i < n; i++) {
            result[i] = result[i - 1] * nums[i - 1];
        }
        
        int right = 1;
        for (int i = n - 1; i >= 0; i--) {
            result[i] = result[i] * right;
            right *= nums[i];
        }
        
        return result;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(1) (không tính mảng kết quả trả về).

---

## 19. Minimum Operations to Reduce X to Zero
**Đề bài chi tiết:** Cho một mảng `nums` và số `x`. Mỗi thao tác, bạn có thể xóa một phần tử ở đầu hoặc cuối mảng và trừ `x` đi giá trị đó. Trả về số thao tác tối thiểu để `x` giảm xuống chính xác 0, hoặc `-1` nếu không thể.
**Phân tích thuật toán:** Việc loại bỏ phần tử ở 2 đầu có tổng bằng `x` tương đương với việc để lại một mảng con liên tục ở giữa có tổng là `total_sum - x`. Bài toán chuyển thành tìm mảng con liên tục dài nhất có tổng bằng `target = total_sum - x`. Ta có thể dùng kĩ thuật Hash Map hoặc Sliding Window (do các số đều không âm) để tìm.
**Mã nguồn Java:**
```java
public class Solution {
    public int minOperations(int[] nums, int x) {
        int totalSum = 0;
        for (int num : nums) totalSum += num;
        
        int target = totalSum - x;
        if (target < 0) return -1;
        if (target == 0) return nums.length;
        
        int maxLen = -1, currSum = 0, left = 0;
        
        for (int right = 0; right < nums.length; right++) {
            currSum += nums[right];
            
            while (currSum > target && left <= right) {
                currSum -= nums[left];
                left++;
            }
            
            if (currSum == target) {
                maxLen = Math.max(maxLen, right - left + 1);
            }
        }
        
        return maxLen == -1 ? -1 : nums.length - maxLen;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(1).

---

## 20. Subarray Product Less Than K
**Đề bài chi tiết:** Cho một mảng số nguyên dương `nums` và một số nguyên `k`. Trả về số lượng mảng con liên tục có tích của các phần tử nhỏ hơn `k`.
**Phân tích thuật toán:** Bài này dùng kĩ thuật Sliding Window thay vì Prefix + Hashing. Lý do là vì tích của các mảng con không âm nên hàm tích số tăng đơn điệu, dễ dàng co giãn cửa sổ tìm kiếm. Đối với Prefix Hashing với tích số, rủi ro tràn số (overflow) xảy ra rất lớn và việc tra cứu không hiệu quả. Dùng 2 con trỏ `left` và `right`, mỗi khi mở rộng `right` làm tích >= `k`, ta tăng `left` để giảm tích. Số mảng con kết thúc tại `right` thỏa mãn là `right - left + 1`.
**Mã nguồn Java:**
```java
public class Solution {
    public int numSubarrayProductLessThanK(int[] nums, int k) {
        if (k <= 1) return 0;
        int count = 0;
        int prod = 1;
        int left = 0;
        
        for (int right = 0; right < nums.length; right++) {
            prod *= nums[right];
            
            while (prod >= k && left <= right) {
                prod /= nums[left];
                left++;
            }
            
            count += right - left + 1;
        }
        return count;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(1).

---

## 21. Maximum Absolute Sum of Any Subarray
**Đề bài chi tiết:** Cho mảng số nguyên `nums`, tìm tổng tuyệt đối lớn nhất của bất kỳ mảng con nào. Tổng tuyệt đối là `|sum|`.
**Phân tích thuật toán:** Một mảng con có thể có tổng dương lớn nhất hoặc tổng âm nhỏ nhất. Ta chỉ cần tìm tổng tiền tố lớn nhất (`maxPrefix`) và tổng tiền tố nhỏ nhất (`minPrefix`). Sự chênh lệch giữa tổng tiền tố lớn nhất và nhỏ nhất chính là tổng tuyệt đối lớn nhất cần tìm: `maxPrefix - minPrefix`. Nếu không dùng Prefix Sum, ta cũng có thể dùng thuật toán Kadane hai lần (một lần tìm max sum, một lần tìm min sum) rồi lấy max absolute.
**Mã nguồn Java:**
```java
public class Solution {
    public int maxAbsoluteSum(int[] nums) {
        int maxPrefix = 0, minPrefix = 0;
        int currentSum = 0;
        
        for (int num : nums) {
            currentSum += num;
            maxPrefix = Math.max(maxPrefix, currentSum);
            minPrefix = Math.min(minPrefix, currentSum);
        }
        
        return maxPrefix - minPrefix;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(1).

---

## 22. Subarray with Given Ratio / Floating Point Hashing
**Đề bài chi tiết:** Trong một số bài toán, bạn cần tìm mảng con có các đặc tính liên quan đến số thực (float/double) hoặc tỉ lệ không đổi. Do sai số của số thực (precision issues), việc sử dụng số thực làm Key trong Hash Map là không an toàn.
**Phân tích thuật toán:** Thay vì Hash một giá trị double, ta nên chuẩn hóa nó dưới dạng phân số tối giản `(tử số, mẫu số)` hoặc sử dụng định dạng chuỗi (Format String) với độ chính xác cố định (ví dụ `String.format("%.6f", value)`). Tuy nhiên, cách tốt nhất luôn là chuyển đổi bài toán về số nguyên. Ví dụ, để kiểm tra tỉ lệ `countA / countB = k`, ta biến đổi thành `countA - k * countB = 0` và áp dụng Prefix Sum cơ bản.
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class Solution {
    // Ví dụ: Tìm mảng con dài nhất có số lượng số 1 gấp k lần số 0
    public int findMaxLengthWithRatio(int[] nums, int k) {
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0, -1);
        int maxLen = 0, sum = 0;
        
        for (int i = 0; i < nums.length; i++) {
            // Biến đổi: count(1) - k * count(0) = 0
            if (nums[i] == 1) sum += 1;
            else if (nums[i] == 0) sum -= k;
            
            if (map.containsKey(sum)) {
                maxLen = Math.max(maxLen, i - map.get(sum));
            } else {
                map.put(sum, i);
            }
        }
        return maxLen;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(N).

---

## 23. XOR Queries of a Subarray
**Đề bài chi tiết:** Cho mảng `arr` và danh sách các truy vấn `queries` dưới dạng `[L, R]`. Trả về mảng kết quả trong đó kết quả của mỗi truy vấn là phép XOR của các phần tử từ `arr[L]` đến `arr[R]`.
**Phân tích thuật toán:** Phép XOR có tính chất tự nghịch đảo: `A XOR A = 0`. Ta có thể tính mảng tiền tố XOR (Prefix XOR), trong đó `prefix[i]` là XOR của các phần tử từ `arr[0]` đến `arr[i-1]`. Giá trị XOR của mảng con từ `L` đến `R` có thể tính nhanh bằng `prefix[R + 1] ^ prefix[L]`.
**Mã nguồn Java:**
```java
public class Solution {
    public int[] xorQueries(int[] arr, int[][] queries) {
        int n = arr.length;
        int[] prefixXor = new int[n + 1];
        
        for (int i = 0; i < n; i++) {
            prefixXor[i + 1] = prefixXor[i] ^ arr[i];
        }
        
        int[] result = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            int L = queries[i][0];
            int R = queries[i][1];
            result[i] = prefixXor[R + 1] ^ prefixXor[L];
        }
        
        return result;
    }
}
```
**Độ phức tạp:** Thời gian tính Prefix O(N), mỗi truy vấn O(1). Tổng thời gian O(N + Q), Không gian O(N).

---

## 24. K-diff Pairs in an Array
**Đề bài chi tiết:** Cho mảng số nguyên `nums` và số nguyên `k`. Trả về số lượng các cặp số `(nums[i], nums[j])` duy nhất trong mảng sao cho `|nums[i] - nums[j]| == k`.
**Phân tích thuật toán:** Đây không phải là bài toán mảng con liên tục nhưng sử dụng chung ý tưởng đếm bằng Hash Map. Ta đếm tần suất xuất hiện của mỗi phần tử. Nếu `k > 0`, ta duyệt qua các key trong Map, nếu `key + k` cũng tồn tại trong Map, ta tìm được 1 cặp. Nếu `k == 0`, ta cần đếm số lượng key có tần suất xuất hiện lớn hơn hoặc bằng 2.
**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int findPairs(int[] nums, int k) {
        if (k < 0) return 0;
        HashMap<Integer, Integer> map = new HashMap<>();
        for (int num : nums) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }
        
        int count = 0;
        for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
            if (k == 0) {
                if (entry.getValue() >= 2) count++;
            } else {
                if (map.containsKey(entry.getKey() + k)) count++;
            }
        }
        return count;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(N).

---

## 25. Count Vowel Strings in Ranges
**Đề bài chi tiết:** Cho một mảng các chuỗi `words` và một ma trận truy vấn `queries` gồm `[L, R]`. Trả về một mảng kết quả, trong đó kết quả thứ `i` là số lượng chuỗi trong phạm vi từ `L` đến `R` bắt đầu và kết thúc bằng một nguyên âm.
**Phân tích thuật toán:** Do có nhiều truy vấn, ta không thể lặp lại việc kiểm tra mỗi lần. Sử dụng mảng Prefix Sum `prefix[i]` để lưu số lượng chuỗi thỏa mãn tính từ đầu đến vị trí `i-1`. Đối với mỗi truy vấn `[L, R]`, số lượng chuỗi thỏa mãn chính là `prefix[R + 1] - prefix[L]`.
**Mã nguồn Java:**
```java
public class Solution {
    public int[] vowelStrings(String[] words, int[][] queries) {
        int n = words.length;
        int[] prefix = new int[n + 1];
        
        for (int i = 0; i < n; i++) {
            prefix[i + 1] = prefix[i] + (isVowelString(words[i]) ? 1 : 0);
        }
        
        int[] ans = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            ans[i] = prefix[queries[i][1] + 1] - prefix[queries[i][0]];
        }
        return ans;
    }
    
    private boolean isVowelString(String s) {
        return isVowel(s.charAt(0)) && isVowel(s.charAt(s.length() - 1));
    }
    
    private boolean isVowel(char c) {
        return c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u';
    }
}
```
**Độ phức tạp:** Thời gian tạo Prefix O(N), truy vấn O(1). Tổng O(N + Q), Không gian O(N).

---

## 26. Range Sum Query - Immutable / 2D Immutable
**Đề bài chi tiết:** Thiết kế một cấu trúc dữ liệu cho phép khởi tạo bằng một mảng 1D (hoặc 2D) và hỗ trợ truy vấn tính tổng của một đoạn (hoặc ma trận con) nhiều lần.
**Phân tích thuật toán:** Đây là bài toán định nghĩa (definition) của Prefix Sum. Đối với 1D: `prefix[i] = prefix[i-1] + nums[i-1]`. Truy vấn `[L, R]` tính bằng `prefix[R+1] - prefix[L]`. Đối với 2D: `prefix[r][c] = prefix[r-1][c] + prefix[r][c-1] - prefix[r-1][c-1] + matrix[r-1][c-1]`.
**Mã nguồn Java:**
```java
public class NumMatrix {
    private int[][] dp;

    public NumMatrix(int[][] matrix) {
        if (matrix.length == 0 || matrix[0].length == 0) return;
        dp = new int[matrix.length + 1][matrix[0].length + 1];
        for (int r = 0; r < matrix.length; r++) {
            for (int c = 0; c < matrix[0].length; c++) {
                dp[r + 1][c + 1] = dp[r + 1][c] + dp[r][c + 1] + matrix[r][c] - dp[r][c];
            }
        }
    }

    public int sumRegion(int row1, int col1, int row2, int col2) {
        return dp[row2 + 1][col2 + 1] - dp[row1][col2 + 1] - dp[row2 + 1][col1] + dp[row1][col1];
    }
}
```
**Độ phức tạp:** Khởi tạo O(R * C), truy vấn O(1), Không gian O(R * C).

---

## 27. Pairs of Songs With Total Durations Divisible by 60
**Đề bài chi tiết:** Cho một danh sách thời lượng các bài hát, tính số cặp bài hát có tổng thời lượng chia hết cho 60.
**Phân tích thuật toán:** Bài toán tương tự bài 3 nhưng thay vì Subarray liên tục, đây là bài toán 2Sum (Cặp phần tử). Ta lấy từng thời lượng modulo 60 (`t % 60`). Ta dùng một mảng tần suất độ dài 60 để đếm. Nếu phần dư hiện tại là `rem`, ta cần tìm số lượng bài hát trước đó có phần dư là `(60 - rem) % 60`. Cập nhật kết quả rồi mới thêm `rem` vào mảng tần suất.
**Mã nguồn Java:**
```java
public class Solution {
    public int numPairsDivisibleBy60(int[] time) {
        int[] remainders = new int[60];
        int count = 0;
        
        for (int t : time) {
            int rem = t % 60;
            int target = (60 - rem) % 60;
            count += remainders[target];
            remainders[rem]++;
        }
        
        return count;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(60) = O(1).

---

## 28. Number of Ways to Split Array
**Đề bài chi tiết:** Cho mảng số nguyên `nums`. Đếm số cách chia mảng thành hai phần (tại vị trí `i`) sao cho phần bên trái có ít nhất một phần tử, phần bên phải có ít nhất một phần tử, và tổng phần bên trái lớn hơn hoặc bằng tổng phần bên phải.
**Phân tích thuật toán:** Tính tổng của tất cả các phần tử trong mảng. Sau đó duyệt mảng từ trái sang phải, cộng dồn `leftSum`. Tổng phần bên phải sẽ là `totalSum - leftSum`. Kiểm tra điều kiện `leftSum >= totalSum - leftSum` để tăng biến đếm. Cần sử dụng kiểu `long` để tránh tràn số.
**Mã nguồn Java:**
```java
public class Solution {
    public int waysToSplitArray(int[] nums) {
        long totalSum = 0;
        for (int num : nums) {
            totalSum += num;
        }
        
        long leftSum = 0;
        int count = 0;
        
        for (int i = 0; i < nums.length - 1; i++) {
            leftSum += nums[i];
            long rightSum = totalSum - leftSum;
            if (leftSum >= rightSum) {
                count++;
            }
        }
        
        return count;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(1).

---

## 29. Grid Illumination
**Đề bài chi tiết:** Cho một lưới `N x N` với các bóng đèn. Một bóng đèn chiếu sáng toàn bộ hàng, cột, và 2 đường chéo đi qua nó. Với một số truy vấn tắt đèn, hãy trả về kết quả lưới có sáng hay không.
**Phân tích thuật toán:** Ta sử dụng Hash Map để đếm số lượng bóng đèn chiếu sáng cho mỗi hàng (`r`), mỗi cột (`c`), mỗi đường chéo chính (`r - c`) và đường chéo phụ (`r + c`). Đây là bài toán Hashing nhiều Prefix Sum/Line. Khi tra cứu một ô, ta chỉ cần kiểm tra xem tổng số bóng đèn trên bất kỳ đường nào đi qua ô đó có lớn hơn 0 hay không. Nếu tắt đèn, ta giảm tần suất tương ứng trong các Hash Map.
**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.HashSet;

public class Solution {
    public int[] gridIllumination(int n, int[][] lamps, int[][] queries) {
        HashMap<Integer, Integer> row = new HashMap<>();
        HashMap<Integer, Integer> col = new HashMap<>();
        HashMap<Integer, Integer> diag1 = new HashMap<>();
        HashMap<Integer, Integer> diag2 = new HashMap<>();
        HashSet<Long> activeLamps = new HashSet<>();
        
        for (int[] lamp : lamps) {
            int r = lamp[0], c = lamp[1];
            long hash = (long) r * n + c;
            if (activeLamps.add(hash)) {
                row.put(r, row.getOrDefault(r, 0) + 1);
                col.put(c, col.getOrDefault(c, 0) + 1);
                diag1.put(r - c, diag1.getOrDefault(r - c, 0) + 1);
                diag2.put(r + c, diag2.getOrDefault(r + c, 0) + 1);
            }
        }
        
        int[] ans = new int[queries.length];
        int[][] dirs = {{-1,-1},{-1,0},{-1,1},{0,-1},{0,0},{0,1},{1,-1},{1,0},{1,1}};
        
        for (int i = 0; i < queries.length; i++) {
            int r = queries[i][0], c = queries[i][1];
            if (row.getOrDefault(r, 0) > 0 || col.getOrDefault(c, 0) > 0 ||
                diag1.getOrDefault(r - c, 0) > 0 || diag2.getOrDefault(r + c, 0) > 0) {
                ans[i] = 1;
            }
            
            for (int[] d : dirs) {
                int nr = r + d[0], nc = c + d[1];
                long hash = (long) nr * n + nc;
                if (activeLamps.contains(hash)) {
                    activeLamps.remove(hash);
                    row.put(nr, row.get(nr) - 1);
                    col.put(nc, col.get(nc) - 1);
                    diag1.put(nr - nc, diag1.get(nr - nc) - 1);
                    diag2.put(nr + nc, diag2.get(nr + nc) - 1);
                }
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:** Thời gian O(L + Q), Không gian O(L) với L là số bóng đèn.

---

## 30. Make Array Strictly Increasing (Advanced State Hashing)
**Đề bài chi tiết:** Cho hai mảng nguyên `arr1` và `arr2`. Bạn có thể thay thế phần tử của `arr1` bằng phần tử của `arr2`. Tìm số thao tác tối thiểu để `arr1` trở nên tăng ngặt.
**Phân tích thuật toán:** Bài toán Quy hoạch động (DP) trong đó không gian trạng thái quá thưa thớt, nếu dùng mảng DP thông thường sẽ vượt quá bộ nhớ. Ta dùng Hash Map để lưu trữ trạng thái DP. Trạng thái `dp[val] = steps` có ý nghĩa: để phần tử cuối cùng của tiền tố hiện tại có giá trị `val`, ta cần ít nhất `steps` thao tác. Ở mỗi bước, ta tạo một Hash Map mới chuyển trạng thái từ Map cũ. `arr2` được sắp xếp để hỗ trợ tìm kiếm nhị phân (chọn phần tử nhỏ nhất lớn hơn phần tử trước đó).
**Mã nguồn Java:**
```java
import java.util.Arrays;
import java.util.HashMap;
import java.util.TreeSet;

public class Solution {
    public int makeArrayIncreasing(int[] arr1, int[] arr2) {
        TreeSet<Integer> set = new TreeSet<>();
        for (int num : arr2) set.add(num);
        
        HashMap<Integer, Integer> dp = new HashMap<>();
        dp.put(-1, 0); // Khởi tạo với giá trị nhỏ nhất tưởng tượng và 0 thao tác
        
        for (int i = 0; i < arr1.length; i++) {
            HashMap<Integer, Integer> nextDp = new HashMap<>();
            for (int key : dp.keySet()) {
                int steps = dp.get(key);
                
                // Giữ nguyên arr1[i] nếu hợp lệ
                if (arr1[i] > key) {
                    nextDp.put(arr1[i], Math.min(nextDp.getOrDefault(arr1[i], Integer.MAX_VALUE), steps));
                }
                
                // Thay thế bằng phần tử nhỏ nhất trong arr2 lớn hơn key
                Integer replaceVal = set.higher(key);
                if (replaceVal != null) {
                    nextDp.put(replaceVal, Math.min(nextDp.getOrDefault(replaceVal, Integer.MAX_VALUE), steps + 1));
                }
            }
            dp = nextDp;
            if (dp.isEmpty()) return -1;
        }
        
        int minSteps = Integer.MAX_VALUE;
        for (int steps : dp.values()) {
            minSteps = Math.min(minSteps, steps);
        }
        return minSteps;
    }
}
```
**Độ phức tạp:** Thời gian O(N * M log M) với M là số phần tử của `arr2`. Không gian O(M).
