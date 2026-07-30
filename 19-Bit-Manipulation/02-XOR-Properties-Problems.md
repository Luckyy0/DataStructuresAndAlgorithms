# 02 - XOR Properties: Problems (Bài tập thực hành)

Dưới đây là 30 bài toán kinh điển và phổ biến nhất tập trung vào tính chất và ứng dụng của toán tử XOR. 10 bài đầu tiên sẽ có hướng dẫn, phân tích và mã nguồn Java chi tiết. Các bài còn lại được tóm tắt ở cuối.

## 1. Single Number (Số duy nhất)
- **Đề bài chi tiết:** Cho một mảng số nguyên không trống `nums`, mọi phần tử đều xuất hiện 2 lần ngoại trừ một phần tử chỉ xuất hiện 1 lần. Hãy tìm phần tử duy nhất đó. Bạn phải thực hiện với độ phức tạp thời gian tuyến tính và không sử dụng thêm bộ nhớ.
- **Phân tích thuật toán:** Dựa vào tính chất `x ^ x = 0` và `x ^ 0 = x`, ta có thể XOR tất cả các phần tử trong mảng lại với nhau. Các cặp số giống nhau sẽ tự triệt tiêu thành `0`, phần tử cuối cùng còn sót lại chính là phần tử duy nhất.
- **Mã nguồn Java:**
```java
class Solution {
    public int singleNumber(int[] nums) {
        int res = 0;
        for (int num : nums) {
            res ^= num;
        }
        return res;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N)`, Không gian: `O(1)`.

## 2. Single Number II (Số duy nhất II)
- **Đề bài chi tiết:** Cho mảng `nums` trong đó mỗi phần tử xuất hiện 3 lần, ngoại trừ một phần tử xuất hiện đúng 1 lần. Tìm phần tử đó.
- **Phân tích thuật toán:** Ta không thể dùng phép XOR trực tiếp cho toàn mảng vì `x ^ x ^ x = x`. Thay vào đó, ta đếm số lượng bit 1 ở từng vị trí (từ 0 đến 31). Với mỗi vị trí, tổng số bit 1 lấy modulo 3. Nếu còn dư, chứng tỏ bit đó thuộc về phần tử xuất hiện 1 lần. Ta dùng OR và phép Shift để khôi phục lại số đó.
- **Mã nguồn Java:**
```java
class Solution {
    public int singleNumber(int[] nums) {
        int ans = 0;
        for (int i = 0; i < 32; i++) {
            int count = 0;
            for (int num : nums) {
                if (((num >> i) & 1) == 1) {
                    count++;
                }
            }
            if (count % 3 != 0) {
                ans |= (1 << i);
            }
        }
        return ans;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N)`, Không gian: `O(1)`. *(Lưu ý: Có thuật toán giải bằng bitmask `ones`, `twos` tối ưu thời gian tốt hơn nhưng phức tạp hơn)*.

## 3. Single Number III (Số duy nhất III)
- **Đề bài chi tiết:** Cho mảng `nums`, có đúng 2 phần tử chỉ xuất hiện 1 lần, các phần tử khác xuất hiện 2 lần. Hãy tìm 2 phần tử đó.
- **Phân tích thuật toán:**
  1. XOR toàn bộ mảng sẽ được kết quả `xorAll = A ^ B` (A và B là 2 số cần tìm).
  2. Vì `A != B`, trong `xorAll` chắc chắn có ít nhất 1 bit `1`. Ta tìm bit 1 ngoài cùng bên phải bằng biểu thức `xorAll & (-xorAll)`.
  3. Dựa vào bit này, ta chia mảng thành 2 nhóm: nhóm có bit này bằng `1` và nhóm bằng `0`. A và B sẽ rơi vào 2 nhóm khác nhau.
  4. Tiếp tục XOR độc lập 2 nhóm sẽ thu được A và B.
- **Mã nguồn Java:**
```java
class Solution {
    public int[] singleNumber(int[] nums) {
        int xorAll = 0;
        for (int num : nums) xorAll ^= num;
        
        int rightmostBit = xorAll & (-xorAll); // Lấy bit 1 khác biệt đầu tiên
        
        int a = 0, b = 0;
        for (int num : nums) {
            if ((num & rightmostBit) != 0) {
                a ^= num;
            } else {
                b ^= num;
            }
        }
        return new int[]{a, b};
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N)`, Không gian: `O(1)`.

## 4. Missing Number (Số bị khuyết)
- **Đề bài chi tiết:** Cho một mảng chứa `n` số phân biệt nằm trong khoảng `[0, n]`. Hãy trả về số nguyên duy nhất nằm trong mảng bị thiếu.
- **Phân tích thuật toán:** Dùng tính chất XOR `x ^ x = 0`. Ta khởi tạo `res = n`. Sau đó duyệt từ `0` đến `n-1`, XOR `res` với `i` (index) và `nums[i]`. Phần tử nào bị thiếu sẽ không được XOR triệt tiêu và đọng lại trong `res`.
- **Mã nguồn Java:**
```java
class Solution {
    public int missingNumber(int[] nums) {
        int n = nums.length;
        int res = n;
        for (int i = 0; i < n; i++) {
            res ^= i ^ nums[i];
        }
        return res;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N)`, Không gian: `O(1)`.

## 5. Find the Original Array of Prefix Xor (Mảng gốc của Prefix XOR)
- **Đề bài chi tiết:** Cho một mảng `pref` kích thước `n`. Biết mảng `pref` được tạo từ `arr` theo công thức `pref[i] = arr[0] ^ arr[1] ^ ... ^ arr[i]`. Hãy trả về mảng gốc `arr`.
- **Phân tích thuật toán:** Từ tính chất đảo ngược của XOR: `pref[i] = pref[i-1] ^ arr[i]`. Từ đó suy ra `arr[i] = pref[i] ^ pref[i-1]`. Áp dụng từ vị trí thứ `1` tới `n-1`. `arr[0]` chính là `pref[0]`.
- **Mã nguồn Java:**
```java
class Solution {
    public int[] findArray(int[] pref) {
        int n = pref.length;
        int[] arr = new int[n];
        arr[0] = pref[0];
        for (int i = 1; i < n; i++) {
            arr[i] = pref[i] ^ pref[i - 1];
        }
        return arr;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N)`, Không gian: `O(N)`.

## 6. Maximum XOR for Each Query (XOR lớn nhất cho mỗi truy vấn)
- **Đề bài chi tiết:** Cho một mảng đã sắp xếp `nums` kích thước `n` và số nguyên `maximumBit`. Thực hiện `n` lần truy vấn. Ở truy vấn `i`, tìm số `k` nhỏ hơn `2^maximumBit` sao cho `XOR_ALL_NUMS ^ k` là lớn nhất. Sau mỗi truy vấn, xóa phần tử cuối cùng của mảng.
- **Phân tích thuật toán:**
  1. Mục tiêu muốn phép XOR đạt tối đa tức là nó tiến tới `mask = (1 << maximumBit) - 1`.
  2. Biết `XOR_ALL ^ k = mask`, suy ra `k = mask ^ XOR_ALL`.
  3. Để tối ưu ta tính XOR tích luỹ của cả mảng từ đầu, sau mỗi truy vấn thì trừ phần tử cuối (`currentXor ^= nums[lastIndex]`).
- **Mã nguồn Java:**
```java
class Solution {
    public int[] getMaximumXor(int[] nums, int maximumBit) {
        int n = nums.length;
        int[] ans = new int[n];
        int mask = (1 << maximumBit) - 1;
        int currentXor = 0;
        
        for (int num : nums) currentXor ^= num;
        
        for (int i = 0; i < n; i++) {
            ans[i] = currentXor ^ mask;
            currentXor ^= nums[n - 1 - i]; // Loại bỏ phần tử cuối
        }
        return ans;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N)`, Không gian: `O(1)` (Không tính mảng trả về).

## 7. XOR Operation in an Array (Toán tử XOR trong mảng)
- **Đề bài chi tiết:** Cho số nguyên `n` và `start`. Định nghĩa mảng `nums` sao cho `nums[i] = start + 2 * i` với `0 <= i < n`. Trả về phép XOR của tất cả các phần tử trong mảng.
- **Phân tích thuật toán:** Ta có thể mô phỏng vòng lặp O(N) bình thường bằng cách duyệt từng phần tử. Tối ưu hơn O(1) có thể làm được bằng các tính chất toán học XOR tuần hoàn, tuy nhiên O(N) là đủ cho giới hạn n <= 1000.
- **Mã nguồn Java:**
```java
class Solution {
    public int xorOperation(int n, int start) {
        int res = 0;
        for (int i = 0; i < n; i++) {
            res ^= (start + 2 * i);
        }
        return res;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N)`, Không gian: `O(1)`.

## 8. Decode XORed Array (Giải mã mảng XOR)
- **Đề bài chi tiết:** Có một mảng `arr` bí mật có độ dài `n`. Mảng này được mã hóa thành `encoded` chiều dài `n-1`, trong đó `encoded[i] = arr[i] ^ arr[i+1]`. Được cung cấp mảng `encoded` và `first = arr[0]`. Giải mã về mảng gốc.
- **Phân tích thuật toán:** Từ định nghĩa: `encoded[i] = arr[i] ^ arr[i+1]`. Suy ra `arr[i+1] = encoded[i] ^ arr[i]`. Vì chúng ta đã có phần tử đầu tiên `arr[0]`, ta tính lần lượt theo chiều thuận.
- **Mã nguồn Java:**
```java
class Solution {
    public int[] decode(int[] encoded, int first) {
        int n = encoded.length;
        int[] arr = new int[n + 1];
        arr[0] = first;
        for (int i = 0; i < n; i++) {
            arr[i + 1] = arr[i] ^ encoded[i];
        }
        return arr;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N)`, Không gian: `O(N)`.

## 9. Minimum Flips to Make a OR b Equal to c (Số lần lật bit tối thiểu để a OR b bằng c)
- **Đề bài chi tiết:** Cho 3 số dương `a`, `b`, `c`. Trả về số lượng thao tác lật (flip bit) tối thiểu vào `a` và `b` để `a OR b == c`.
- **Phân tích thuật toán:** So sánh song song trên từng cặp bit. Nếu bit của `c` là 0, thì bit của `a` và `b` bắt buộc phải là 0 (nếu chúng đang 1 thì lật cả hai, cộng số bit 1 của a và b). Nếu bit của `c` là 1, thì `a` hoặc `b` phải có ít nhất một bit 1. Nếu cả hai là 0, ta lật 1 trong 2 biến, tức là cộng thêm 1 thao tác.
- **Mã nguồn Java:**
```java
class Solution {
    public int minFlips(int a, int b, int c) {
        int flips = 0;
        for (int i = 0; i < 32; i++) {
            int bitA = (a >> i) & 1;
            int bitB = (b >> i) & 1;
            int bitC = (c >> i) & 1;
            
            if (bitC == 0) {
                flips += (bitA + bitB);
            } else {
                if (bitA == 0 && bitB == 0) {
                    flips++;
                }
            }
        }
        return flips;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(1)` (Vì luôn lặp 32 lần), Không gian: `O(1)`.

## 10. Hamming Distance (Khoảng cách Hamming)
- **Đề bài chi tiết:** Khoảng cách Hamming giữa hai số nguyên là số lượng vị trí mà các bit tương ứng ở vị trí đó khác nhau. Tính khoảng cách Hamming của `x` và `y`.
- **Phân tích thuật toán:** Chỉ cần thực hiện `x ^ y`. Kết quả này sẽ chứa các bit `1` ở đúng các vị trí mà `x` và `y` khác biệt. Cuối cùng sử dụng vòng lặp đếm số lượng bit 1 (hoặc `Integer.bitCount`).
- **Mã nguồn Java:**
```java
class Solution {
    public int hammingDistance(int x, int y) {
        int xor = x ^ y;
        int distance = 0;
        while (xor != 0) {
            distance += (xor & 1);
            xor >>= 1;
        }
        return distance;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(1)` (Giới hạn 32 bit), Không gian: `O(1)`.


## 11. Subsets (Tập con)
- **Đề bài chi tiết:** Cho mảng số nguyên `nums` chứa các phần tử phân biệt, hãy trả về tất cả các tập con có thể có (powerset).
- **Phân tích thuật toán:** Một mảng kích thước `n` sẽ có `2^n` tập con. Ta có thể dùng số nguyên từ `0` đến `2^n - 1` để đại diện cho các tập con. Nếu bit thứ `j` của số nguyên `i` là 1, ta đưa phần tử `nums[j]` vào tập con hiện tại (tương tự việc quản lý trạng thái có/không bằng bitmask).
- **Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        int n = nums.length;
        int totalSubsets = 1 << n; // 2^n
        for (int i = 0; i < totalSubsets; i++) {
            List<Integer> subset = new ArrayList<>();
            for (int j = 0; j < n; j++) {
                if (((i >> j) & 1) == 1) {
                    subset.add(nums[j]);
                }
            }
            result.add(subset);
        }
        return result;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N * 2^N)`, Không gian: `O(1)` (Không tính không gian lưu trữ kết quả).

## 12. Find the Difference (Tìm ký tự khác biệt)
- **Đề bài chi tiết:** Cho hai chuỗi `s` và `t`. Chuỗi `t` được tạo ra bằng cách xáo trộn ngẫu nhiên chuỗi `s` và thêm vào một chữ cái mới ở một vị trí bất kỳ. Tìm chữ cái đã được thêm vào chuỗi `t`.
- **Phân tích thuật toán:** Tương tự như bài "Single Number", tất cả các ký tự trong `s` đều xuất hiện trong `t`. Nếu ta XOR giá trị ASCII của tất cả các ký tự trong cả hai chuỗi, các ký tự giống nhau sẽ triệt tiêu thành 0. Giá trị cuối cùng còn lại chính là ký tự độc nhất được thêm vào.
- **Mã nguồn Java:**
```java
class Solution {
    public char findTheDifference(String s, String t) {
        char res = 0;
        for (char c : s.toCharArray()) res ^= c;
        for (char c : t.toCharArray()) res ^= c;
        return res;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N)`, Không gian: `O(1)`.

## 13. Maximum XOR of Two Numbers in an Array (XOR lớn nhất của hai số trong mảng)
- **Đề bài chi tiết:** Cho mảng số nguyên `nums`. Tìm giá trị lớn nhất của `nums[i] ^ nums[j]` với `0 <= i <= j < n`.
- **Phân tích thuật toán:** Để kết quả XOR đạt cực đại, ta luôn mong muốn các bit ở vị trí cao nhất có giá trị `1`. Ta có thể duyệt từ bit cao nhất xuống (31 đến 0). Tại mỗi bit, ta giả sử bit đó có thể bật thành 1 ở kết quả (gọi là `target`). Lấy tất cả các tiền tố của các số đưa vào một `HashSet` và kiểm tra xem có hai tiền tố nào XOR với nhau ra `target` không (`prefix1 ^ target = prefix2`). Cấu trúc Trie cũng là một giải pháp rất phổ biến cho bài này.
- **Mã nguồn Java:**
```java
class Solution {
    public int findMaximumXOR(int[] nums) {
        int max = 0, mask = 0;
        for (int i = 31; i >= 0; i--) {
            mask |= (1 << i);
            Set<Integer> set = new HashSet<>();
            for (int num : nums) {
                set.add(num & mask);
            }
            int greedyTry = max | (1 << i);
            for (int prefix : set) {
                if (set.contains(greedyTry ^ prefix)) {
                    max = greedyTry;
                    break;
                }
            }
        }
        return max;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N)`, Không gian: `O(N)`.

## 14. XOR Queries of a Subarray (Truy vấn XOR của mảng con)
- **Đề bài chi tiết:** Cho mảng `arr` chứa các số nguyên dương và mảng `queries` trong đó `queries[i] = [L_i, R_i]`. Với mỗi truy vấn `i`, hãy tính XOR của các phần tử từ `L_i` đến `R_i` (tức là `arr[L_i] ^ arr[L_i+1] ^ ... ^ arr[R_i]`). Trả về mảng kết quả của tất cả truy vấn.
- **Phân tích thuật toán:** Sử dụng kỹ thuật Prefix XOR (XOR cộng dồn). Gọi `prefix[i]` là XOR của tất cả phần tử từ `0` đến `i`. Khi đó, phép tính XOR từ đoạn `L` đến `R` sẽ bằng `prefix[R] ^ prefix[L-1]`. Nếu `L == 0` thì kết quả chính là `prefix[R]`.
- **Mã nguồn Java:**
```java
class Solution {
    public int[] xorQueries(int[] arr, int[][] queries) {
        int n = arr.length;
        int[] prefix = new int[n];
        prefix[0] = arr[0];
        for (int i = 1; i < n; i++) {
            prefix[i] = prefix[i - 1] ^ arr[i];
        }
        
        int[] res = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            int L = queries[i][0];
            int R = queries[i][1];
            res[i] = L == 0 ? prefix[R] : prefix[R] ^ prefix[L - 1];
        }
        return res;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N + Q)` (với Q là số lượng truy vấn), Không gian: `O(N)`.

## 15. Count Triplets That Can Form Two Arrays of Equal XOR (Đếm số bộ ba thỏa mãn)
- **Đề bài chi tiết:** Cho mảng số nguyên `arr`. Đếm số lượng bộ ba `(i, j, k)` sao cho `0 <= i < j <= k < arr.length` và `a == b`, trong đó `a = arr[i] ^ ... ^ arr[j-1]` và `b = arr[j] ^ ... ^ arr[k]`.
- **Phân tích thuật toán:** Yêu cầu `a == b` tương đương với `a ^ b == 0`. Tức là XOR của toàn bộ đoạn từ `i` đến `k` phải bằng 0 (`arr[i] ^ ... ^ arr[k] == 0`). Với mỗi cặp `(i, k)` thỏa mãn điều kiện này, mọi giá trị `j` nằm giữa `i` và `k` đều thỏa mãn. Do đó số lượng `j` có thể chọn là `k - i`. Ta chỉ cần lồng 2 vòng lặp để tìm mọi đoạn `[i, k]` có XOR bằng 0.
- **Mã nguồn Java:**
```java
class Solution {
    public int countTriplets(int[] arr) {
        int count = 0;
        int n = arr.length;
        for (int i = 0; i < n; i++) {
            int currentXor = arr[i];
            for (int k = i + 1; k < n; k++) {
                currentXor ^= arr[k];
                if (currentXor == 0) {
                    count += (k - i);
                }
            }
        }
        return count;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N^2)`, Không gian: `O(1)`.

## 16. Minimize XOR (Giảm thiểu XOR)
- **Đề bài chi tiết:** Cho 2 số nguyên dương `num1` và `num2`. Hãy tìm số nguyên `x` sao cho `x` có cùng số lượng set bit (bit 1) với `num2` và giá trị của `x ^ num1` là nhỏ nhất có thể.
- **Phân tích thuật toán:** Để `x ^ num1` là nhỏ nhất, `x` nên có các bit 1 ở cùng vị trí với các bit 1 từ cao nhất (từ trái qua) của `num1`. Gọi số lượng bit 1 cần thiết là `c = bitCount(num2)`. Đầu tiên, lấp bit 1 vào các vị trí đang là 1 của `num1` từ trái sang phải. Nếu `x` vẫn còn thiếu bit 1 (chưa đủ `c`), ta lấp thêm vào các vị trí 0 từ phải sang trái (vị trí thấp nhất) để làm tăng giá trị ít nhất.
- **Mã nguồn Java:**
```java
class Solution {
    public int minimizeXor(int num1, int num2) {
        int count = Integer.bitCount(num2);
        int res = 0;
        for (int i = 31; i >= 0 && count > 0; i--) {
            if ((num1 & (1 << i)) != 0) {
                res |= (1 << i);
                count--;
            }
        }
        for (int i = 0; i <= 31 && count > 0; i++) {
            if ((res & (1 << i)) == 0) {
                res |= (1 << i);
                count--;
            }
        }
        return res;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(1)`, Không gian: `O(1)`.

## 17. Maximum XOR With an Element From Array (XOR lớn nhất với một phần tử từ mảng)
- **Đề bài chi tiết:** Cho mảng `nums` và mảng các truy vấn `queries`, trong đó `queries[i] = [x_i, m_i]`. Với mỗi truy vấn, trả về giá trị lớn nhất của `x_i ^ nums[j]` sao cho `nums[j] <= m_i`. Nếu không có `nums[j]` nào thỏa mãn, trả về -1.
- **Phân tích thuật toán:** Dùng Offline Queries kết hợp với Trie. Sắp xếp `nums` và `queries` (theo `m_i` tăng dần). Duyệt qua từng truy vấn, nạp tất cả các phần tử `nums[j] <= m_i` vào một Trie nhị phân. Sau đó trên Trie, tìm phần tử XOR với `x_i` cho kết quả lớn nhất.
- **Mã nguồn Java:**
```java
class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[2];
    }
    TrieNode root = new TrieNode();
    
    private void insert(int num) {
        TrieNode node = root;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if (node.children[bit] == null) node.children[bit] = new TrieNode();
            node = node.children[bit];
        }
    }
    
    private int getMaxXor(int num) {
        if (root.children[0] == null && root.children[1] == null) return -1;
        TrieNode node = root;
        int max = 0;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            int toggled = 1 - bit;
            if (node.children[toggled] != null) {
                max |= (1 << i);
                node = node.children[toggled];
            } else {
                node = node.children[bit];
            }
        }
        return max;
    }
    
    public int[] maximizeXor(int[] nums, int[][] queries) {
        Arrays.sort(nums);
        int q = queries.length;
        int[][] qWithIndex = new int[q][3];
        for (int i = 0; i < q; i++) {
            qWithIndex[i] = new int[]{queries[i][0], queries[i][1], i};
        }
        Arrays.sort(qWithIndex, (a, b) -> Integer.compare(a[1], b[1]));
        
        int[] res = new int[q];
        int j = 0;
        for (int i = 0; i < q; i++) {
            int x = qWithIndex[i][0], m = qWithIndex[i][1], idx = qWithIndex[i][2];
            while (j < nums.length && nums[j] <= m) {
                insert(nums[j++]);
            }
            res[idx] = getMaxXor(x);
        }
        return res;
    }
}
```
- **Độ phức tạp:** Thời gian: `O((N + Q) \log(\text{MAX})) + O(N \log N + Q \log Q)`, Không gian: `O(N \log(\text{MAX}) + Q)`.

## 18. Missing Number In Arithmetic Progression (Số thiếu trong cấp số cộng)
- **Đề bài chi tiết:** Cho một mảng `arr` đại diện cho một cấp số cộng đã bị xóa đi chính xác một phần tử (không phải ở hai đầu). Hãy tìm phần tử bị thiếu.
- **Phân tích thuật toán:** Một biến thể của số thiếu. Số lượng phần tử ban đầu là `n + 1`. Khoảng cách `d` (công sai) có thể tính bằng `(arr[n - 1] - arr[0]) / n`. Ta có thể tính XOR của tất cả các phần tử lý tưởng tạo thành cấp số cộng đó, rồi đem XOR với toàn bộ các phần tử hiện tại trong mảng `arr`. Kết quả sẽ loại bỏ được các số trùng và giữ lại phần tử bị thiếu.
- **Mã nguồn Java:**
```java
class Solution {
    public int missingNumber(int[] arr) {
        int n = arr.length;
        int d = (arr[n - 1] - arr[0]) / n;
        int expectedXor = 0;
        int actualXor = 0;
        
        for (int i = 0; i <= n; i++) {
            expectedXor ^= (arr[0] + i * d);
        }
        for (int num : arr) {
            actualXor ^= num;
        }
        
        return expectedXor ^ actualXor;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N)`, Không gian: `O(1)`.

## 19. Number of Wonderful Substrings (Số lượng chuỗi con tuyệt vời)
- **Đề bài chi tiết:** Chuỗi "tuyệt vời" là chuỗi có nhiều nhất 1 ký tự xuất hiện với số lần lẻ. Cho một chuỗi `word` chỉ chứa các ký tự từ `'a'` đến `'j'`, hãy trả về số lượng chuỗi con tuyệt vời có thể được tạo ra.
- **Phân tích thuật toán:** Ta dùng bitmask (số nguyên 10 bit) để lưu tính chẵn lẻ của 10 ký tự `a-j`. (0 là chẵn, 1 là lẻ). Khi duyệt qua chuỗi, tính prefix XOR mask. Nếu 2 prefix mask giống hệt nhau, chuỗi con ở giữa có các ký tự chẵn. Nếu 2 prefix mask khác nhau đúng 1 bit, chuỗi con có đúng 1 ký tự xuất hiện lẻ lần. Ta dùng mảng đếm tần suất các mask để cộng dồn kết quả nhanh chóng.
- **Mã nguồn Java:**
```java
class Solution {
    public long wonderfulSubstrings(String word) {
        long[] count = new long[1024]; // 2^10 trạng thái
        count[0] = 1;
        long res = 0;
        int mask = 0;
        
        for (char c : word.toCharArray()) {
            mask ^= (1 << (c - 'a')); // Cập nhật tính chẵn lẻ
            
            // Trường hợp toàn bộ chẵn
            res += count[mask];
            
            // Trường hợp có 1 ký tự lẻ
            for (int i = 0; i < 10; i++) {
                res += count[mask ^ (1 << i)];
            }
            
            count[mask]++;
        }
        return res;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N)`, Không gian: `O(1)` (Do mảng có giới hạn 1024).

## 20. Palindrome Permutation (Hoán vị đối xứng - Bitmask Version)
- **Đề bài chi tiết:** Cho một chuỗi `s` (thường chứa các chữ cái in thường), xác định xem có thể hoán vị các ký tự của nó để tạo thành một chuỗi đối xứng (palindrome) hay không.
- **Phân tích thuật toán:** Một chuỗi có thể hoán vị thành chuỗi đối xứng nếu nó có tối đa 1 ký tự xuất hiện với số lần lẻ. Ta có thể gán cho mỗi ký tự một bit. Mỗi lần gặp một ký tự, ta dùng thao tác XOR để bật/tắt (toggle) bit tương ứng. Cuối cùng, để kiểm tra số đó có tối đa 1 bit là `1` hay không, ta sử dụng mẹo bitwise `(mask & (mask - 1)) == 0`.
- **Mã nguồn Java:**
```java
class Solution {
    public boolean canPermutePalindrome(String s) {
        int mask = 0;
        for (char c : s.toCharArray()) {
            mask ^= (1 << (c - 'a'));
        }
        // Kiểm tra mask bằng 0 (chẵn toàn bộ) hoặc chứa đúng 1 bit 1
        return (mask & (mask - 1)) == 0;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N)`, Không gian: `O(1)`.

## 21. XOR Sum of All Pairs Bitwise AND
- **Đề bài chi tiết:** Cho hai mảng số nguyên `arr1` và `arr2`. Tính giá trị biểu thức XOR của `(arr1[i] & arr2[j])` với mọi cặp `(i, j)`.
- **Phân tích thuật toán:** Nếu tính toán thủ công từng cặp sẽ mất `O(N * M)`. Tuy nhiên, ta có thể áp dụng tính chất phân phối của phép AND đối với phép XOR: `(a & b) ^ (a & c) = a & (b ^ c)`. Nhờ vậy, biểu thức tổng quát có thể được rút gọn. Ta XOR toàn bộ mảng `arr1` được `XOR1`, XOR toàn bộ mảng `arr2` được `XOR2`. Kết quả cuối cùng chính là `XOR1 & XOR2`.
- **Mã nguồn Java:**
```java
class Solution {
    public int getXORSum(int[] arr1, int[] arr2) {
        int xor1 = 0, xor2 = 0;
        for (int num : arr1) xor1 ^= num;
        for (int num : arr2) xor2 ^= num;
        return xor1 & xor2;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N + M)`, Không gian: `O(1)`.

## 22. Subarray XOR equal to K (Biến thể Prefix XOR)
- **Đề bài chi tiết:** Cho mảng `arr` và một số nguyên `target`. Hãy tìm số lượng mảng con không rỗng có giá trị XOR của tất cả phần tử bằng với `target`.
- **Phân tích thuật toán:** Bài toán này là phiên bản Prefix XOR thay vì Prefix Sum. Gọi `prefixXor[i]` là XOR từ `0` đến `i`. Nếu một mảng con từ chỉ số `j` đến `i` có XOR bằng `target`, thì `prefixXor[i] ^ prefixXor[j-1] == target`, tương đương với `prefixXor[j-1] == prefixXor[i] ^ target`. Dùng một HashMap để đếm tần suất của các giá trị Prefix XOR xuất hiện trong quá khứ, ta có thể tra cứu nhanh chóng mảng con thỏa mãn trong thời gian hằng số.
- **Mã nguồn Java:**
```java
class Solution {
    public int subarrayXorSum(int[] arr, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, 1); // Khởi tạo mảng tiền tố rỗng với XOR = 0
        int currentXor = 0;
        int count = 0;
        for (int num : arr) {
            currentXor ^= num;
            count += map.getOrDefault(currentXor ^ target, 0);
            map.put(currentXor, map.getOrDefault(currentXor, 0) + 1);
        }
        return count;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N)`, Không gian: `O(N)`.

## 23. Binary Prefix Divisible By 5
- **Đề bài chi tiết:** Cho mảng nhị phân `nums`. Mỗi mảng con tiền tố từ `0` đến `i` tạo thành một số nguyên nhị phân. Hãy kiểm tra xem số nguyên này có chia hết cho 5 hay không và trả về mảng kết quả dạng boolean.
- **Phân tích thuật toán:** Mỗi khi duyệt qua một bit mới, số nguyên hiện tại sẽ được dịch trái 1 bit (nhân 2) và cộng thêm bit mới. Để tránh tràn số học (integer overflow), ta chỉ cần lấy modulo 5 ở mỗi bước (vì tích lũy phần dư là đủ xác định tính chia hết). Phép toán bitwise có thể được sử dụng: `remainder = (remainder << 1 | bit) % 5`.
- **Mã nguồn Java:**
```java
class Solution {
    public List<Boolean> prefixesDivBy5(int[] nums) {
        List<Boolean> res = new ArrayList<>();
        int remainder = 0;
        for (int bit : nums) {
            remainder = ((remainder << 1) | bit) % 5;
            res.add(remainder == 0);
        }
        return res;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N)`, Không gian: `O(N)` (Để lưu trữ mảng kết quả).

## 24. Beautiful Arrangement (Bitmask)
- **Đề bài chi tiết:** Cho số nguyên `n`. Tính số lượng cách hoán vị các số từ `1` đến `n` sao cho phần tử thứ `i` (đánh số từ 1) thỏa mãn: hoặc nó chia hết cho `i`, hoặc `i` chia hết cho nó.
- **Phân tích thuật toán:** Vì `n` nhỏ (thường `n <= 15`), ta có thể dùng quay lui (Backtracking) kết hợp quy hoạch động trạng thái Bitmask để đánh dấu các số đã sử dụng. Nếu bit thứ `j` trong `mask` là 1, tức là số `j+1` đã được dùng. 
- **Mã nguồn Java:**
```java
class Solution {
    public int countArrangement(int n) {
        int[] memo = new int[1 << n];
        Arrays.fill(memo, -1);
        return dfs(n, 1, 0, memo);
    }
    
    private int dfs(int n, int pos, int mask, int[] memo) {
        if (pos > n) return 1; // Đã sắp xếp xong cấu hình
        if (memo[mask] != -1) return memo[mask];
        
        int count = 0;
        for (int i = 1; i <= n; i++) {
            // Nếu số thứ i chưa dùng và thỏa mãn điều kiện chia hết
            if ((mask & (1 << (i - 1))) == 0 && (i % pos == 0 || pos % i == 0)) {
                count += dfs(n, pos + 1, mask | (1 << (i - 1)), memo);
            }
        }
        return memo[mask] = count;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N * 2^N)`, Không gian: `O(2^N)`.

## 25. Finding the Rightmost Set Bit Series (Thao tác với bit phải cùng)
- **Đề bài chi tiết:** Các thao tác kinh điển đối với cấu trúc nhị phân của số nguyên, liên quan tới việc trích xuất và xoá "Rightmost Set Bit" (bit 1 đầu tiên từ bên phải sang).
- **Phân tích thuật toán:** Các công thức thao tác trên bit:
  1. Lấy bit 1 phải cùng: `n & -n`. (Bởi vì `-n` trong bù 2 được biểu diễn là `~n + 1`, nó đảo toàn bộ bit phía trước bit 1 đầu tiên).
  2. Xóa bit 1 phải cùng: `n & (n - 1)`.
  3. Kiểm tra xem `n` có phải là lũy thừa của 2 không: `n > 0 && (n & (n - 1)) == 0`.
- **Mã nguồn Java:**
```java
class BitwiseTricks {
    public int getRightmostSetBit(int n) {
        return n & -n;
    }
    
    public int clearRightmostSetBit(int n) {
        return n & (n - 1);
    }
    
    public boolean isPowerOfTwo(int n) {
        return n > 0 && (n & (n - 1)) == 0;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(1)`, Không gian: `O(1)`.

## 26. Swap Two Variables (Trick Question)
- **Đề bài chi tiết:** Tráo đổi giá trị của hai biến nguyên `a` và `b` mà không sử dụng thêm biến tạm (temporary variable).
- **Phân tích thuật toán:** Áp dụng liên tiếp phép toán XOR với tính chất `x ^ x = 0`.
  - `a = a ^ b`
  - `b = a ^ b` (Lúc này `b = (a ^ b) ^ b = a`)
  - `a = a ^ b` (Lúc này `a = (a ^ b) ^ a = b`)
  *Lưu ý:* Tuy đây là một mẹo phỏng vấn thú vị, nhưng trong thực tế không được khuyến khích vì làm mã nguồn khó đọc và có thể gặp lỗi mất dữ liệu nếu `a` và `b` trỏ tới cùng một địa chỉ bộ nhớ (ví dụ: cùng 1 vị trí mảng `arr[i] ^= arr[i]`).
- **Mã nguồn Java:**
```java
class Solution {
    public void swap(int[] arr, int i, int j) {
        if (i != j) { // Bắt buộc kiểm tra để tránh làm mất dữ liệu thành 0
            arr[i] ^= arr[j];
            arr[j] ^= arr[i];
            arr[i] ^= arr[j];
        }
    }
}
```
- **Độ phức tạp:** Thời gian: `O(1)`, Không gian: `O(1)`.

## 27. Shortest Path Visiting All Nodes (Bitmask DP / BFS)
- **Đề bài chi tiết:** Cho một đồ thị vô hướng, tìm độ dài của đường đi ngắn nhất thăm tất cả các đỉnh ít nhất một lần. Có thể bắt đầu và kết thúc ở đỉnh bất kỳ, và có thể đi qua lại đỉnh đã đi.
- **Phân tích thuật toán:** Do số lượng đỉnh nhỏ (N <= 12), đây là bài toán thích hợp cho BFS kết hợp Bitmask. Trạng thái BFS bao gồm đỉnh hiện tại `u` và một số nguyên `mask` đại diện cho các đỉnh đã thăm. Đích đến là trạng thái có `mask == (1 << n) - 1`.
- **Mã nguồn Java:**
```java
class Solution {
    public int shortestPathLength(int[][] graph) {
        int n = graph.length;
        if (n == 1) return 0;
        
        Queue<int[]> queue = new LinkedList<>();
        boolean[][] visited = new boolean[n][1 << n];
        
        // Ta có thể bắt đầu tại bất kỳ đỉnh nào
        for (int i = 0; i < n; i++) {
            queue.offer(new int[]{i, 1 << i});
            visited[i][1 << i] = true;
        }
        
        int steps = 0;
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                int[] curr = queue.poll();
                int u = curr[0], mask = curr[1];
                
                if (mask == (1 << n) - 1) return steps; // Đã thăm hết
                
                for (int v : graph[u]) {
                    int nextMask = mask | (1 << v);
                    if (!visited[v][nextMask]) {
                        visited[v][nextMask] = true;
                        queue.offer(new int[]{v, nextMask});
                    }
                }
            }
            steps++;
        }
        return -1;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N * 2^N)`, Không gian: `O(N * 2^N)`.

## 28. Minimum Number of Flips to Convert Binary Matrix to Zero Matrix
- **Đề bài chi tiết:** Cho một ma trận nhị phân. Ở mỗi bước, bạn có thể lật một ô (0 thành 1, 1 thành 0), việc này cũng tự động lật luôn các ô kề cạnh nó (trên, dưới, trái, phải). Tìm số bước tối thiểu để biến toàn bộ ma trận thành 0.
- **Phân tích thuật toán:** Kích thước ma trận rất nhỏ (thường tối đa 3x3), ta có thể nén trạng thái ma trận thành 1 số nguyên (tối đa 9 bit). Sử dụng BFS để thử lật tất cả các ô từ trạng thái ban đầu, kết hợp với XOR để lật bit. Mục tiêu là tới được trạng thái `0`.
- **Mã nguồn Java:**
```java
class Solution {
    public int minFlips(int[][] mat) {
        int m = mat.length, n = mat[0].length;
        int start = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                start |= (mat[i][j] << (i * n + j));
            }
        }
        if (start == 0) return 0;
        
        Queue<Integer> queue = new LinkedList<>();
        Set<Integer> visited = new HashSet<>();
        queue.offer(start); visited.add(start);
        
        int steps = 0;
        int[] dirs = {0, 0, 1, 0, -1, 0}; // 4 hướng kề cạnh và chính nó
        
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                int curr = queue.poll();
                if (curr == 0) return steps;
                
                for (int r = 0; r < m; r++) {
                    for (int c = 0; c < n; c++) {
                        int next = curr;
                        for (int d = 0; d < 5; d++) {
                            int nr = r + dirs[d], nc = c + dirs[d+1];
                            if (nr >= 0 && nr < m && nc >= 0 && nc < n) {
                                next ^= (1 << (nr * n + nc)); // Lật bit bằng XOR
                            }
                        }
                        if (visited.add(next)) queue.offer(next);
                    }
                }
            }
            steps++;
        }
        return -1;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(2^{M \times N})` cho số trạng thái, Không gian: `O(2^{M \times N})`.

## 29. XOR of All Subarrays
- **Đề bài chi tiết:** Cho mảng số nguyên `nums` có kích thước `n`. Tính giá trị XOR của TẤT CẢ các mảng con liên tiếp trong `nums`.
- **Phân tích thuật toán:** Bài toán quy về tần suất. Một phần tử ở chỉ số `i` (0-indexed) sẽ xuất hiện trong `(i + 1) * (n - i)` mảng con. Nếu số lần xuất hiện này là chẵn, nó sẽ bị triệt tiêu khi XOR (thành 0). Nếu lẻ, nó sẽ đọng lại ở kết quả cuối cùng. Bằng quan sát toán học, nếu `n` là chẵn, công thức `(i + 1) * (n - i)` sẽ luôn cho ra số chẵn với mọi `i`, kết quả cuối cùng luôn luôn là 0. Nếu `n` lẻ, chỉ các phần tử ở vị trí chẵn (0, 2, 4...) mới xuất hiện với số lần lẻ.
- **Mã nguồn Java:**
```java
class Solution {
    public int xorOfAllSubarrays(int[] nums) {
        int n = nums.length;
        if (n % 2 == 0) return 0; // Mảng kích thước chẵn => luôn 0
        
        int res = 0;
        // Chỉ XOR các số nằm ở index chẵn
        for (int i = 0; i < n; i += 2) {
            res ^= nums[i];
        }
        return res;
    }
}
```
- **Độ phức tạp:** Thời gian: `O(N)`, Không gian: `O(1)`.

## 30. Kth Largest XOR Coordinate Value
- **Đề bài chi tiết:** Cho ma trận `matrix`. Tính XOR tiền tố 2D cho mỗi ô (XOR của tất cả phần tử trong hình chữ nhật từ ô `(0,0)` đến `(i,j)`). Tìm giá trị lớn thứ K trong tất cả các mảng XOR tiền tố đó.
- **Phân tích thuật toán:** Tương tự như mảng cộng dồn 2D (Prefix Sum 2D). Công thức tính Prefix XOR 2D: `prefix[i][j] = matrix[i-1][j-1] ^ prefix[i-1][j] ^ prefix[i][j-1] ^ prefix[i-1][j-1]`.
Ta tạo mảng hoặc ma trận DP để lưu toàn bộ các giá trị, sau đó dùng ArrayList rồi sắp xếp ngược, hoặc dùng Cấu trúc Min-Heap (PriorityQueue) để lấy phần tử lớn thứ K.
- **Mã nguồn Java:**
```java
class Solution {
    public int kthLargestValue(int[][] matrix, int k) {
        int m = matrix.length;
        int n = matrix[0].length;
        int[][] dp = new int[m + 1][n + 1];
        List<Integer> list = new ArrayList<>();
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                // Tính XOR hình chữ nhật bằng nguyên lý bù trừ
                dp[i][j] = matrix[i - 1][j - 1] ^ dp[i - 1][j] ^ dp[i][j - 1] ^ dp[i - 1][j - 1];
                list.add(dp[i][j]);
            }
        }
        
        Collections.sort(list, Collections.reverseOrder());
        return list.get(k - 1);
    }
}
```
- **Độ phức tạp:** Thời gian: `O(M \times N \log(M \times N))` do bước sắp xếp mảng, Không gian: `O(M \times N)`.
