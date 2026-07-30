# 30 Bài Tập Thao Tác Bit Cơ Bản (Basic Bit Operations Problems)

Dưới đây là danh sách 30 bài tập về Thao tác Bit. 10 bài đầu tiên sẽ có đầy đủ đề bài, phân tích, mã nguồn Java và độ phức tạp. 20 bài còn lại được tóm tắt.

---

## 1. Number of 1 Bits (Hamming Weight)

### Đề bài chi tiết
Viết một hàm nhận vào một số nguyên dương vô hướng (unsigned integer) và trả về số lượng bit '1' có trong biểu diễn nhị phân của số đó (còn được gọi là Hamming weight).

### Phân tích thuật toán
- **Cách 1 (Dịch bit)**: Lặp qua từng bit (32 lần), kiểm tra bit cuối bằng `n & 1`, sau đó dịch phải `n >>> 1`.
- **Cách 2 (Brian Kernighan's Algorithm)**: Dùng công thức `n &= (n - 1)`. Phép toán này sẽ luôn xóa đi bit '1' ở vị trí thấp nhất (bên phải cùng). Vòng lặp chỉ chạy số lần bằng số lượng bit '1', nên hiệu quả hơn cho số thưa bit '1'.

### Mã nguồn Java
```java
public class Solution {
    // You need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int count = 0;
        while (n != 0) {
            n &= (n - 1); // Xóa bit 1 thấp nhất
            count++;
        }
        return count;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(k)$, với $k$ là số lượng bit 1 trong $n$. Trong trường hợp xấu nhất là $O(32) = O(1)$.
- **Không gian**: $O(1)$.

---

## 2. Power of Two

### Đề bài chi tiết
Cho một số nguyên $n$, hãy kiểm tra xem $n$ có phải là lũy thừa của 2 hay không. Số nguyên dương $n$ là lũy thừa của 2 nếu tồn tại một số nguyên $x$ sao cho $n = 2^x$.

### Phân tích thuật toán
Một số là lũy thừa của 2 (và lớn hơn 0) thì trong biểu diễn nhị phân của nó chỉ có duy nhất một bit 1.
Sử dụng mẹo xóa bit 1 thấp nhất `n & (n - 1)`. Nếu $n$ chỉ có 1 bit '1', thì sau thao tác này $n$ sẽ bằng 0.

### Mã nguồn Java
```java
public class Solution {
    public boolean isPowerOfTwo(int n) {
        if (n <= 0) return false;
        return (n & (n - 1)) == 0;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(1)$.
- **Không gian**: $O(1)$.

---

## 3. Number Complement

### Đề bài chi tiết
Phần bù (complement) của một số nguyên là số được tạo ra bằng cách lật ngược các bit 0 thành 1 và ngược lại trong biểu diễn nhị phân của nó (không tính các số 0 dẫn đầu). Cho số nguyên `num`, hãy trả về phần bù của nó.

### Phân tích thuật toán
Ta cần tạo ra một mặt nạ (mask) có độ dài bằng với số bit hợp lệ của `num`, toàn là số 1.
Ví dụ: `num` = 5 (101). Mask cần là 111 (7).
Kết quả sẽ là `num ^ mask` (101 ^ 111 = 010).
Cách tạo mask: Lặp và dịch bit trái mask dần cho đến khi lớn hơn `num`.

### Mã nguồn Java
```java
public class Solution {
    public int findComplement(int num) {
        int mask = ~0;
        while ((num & mask) != 0) {
            mask <<= 1;
        }
        return ~mask & ~num; // Cách khác: int m = (Integer.highestOneBit(num) << 1) - 1; return num ^ m;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(1)$ (tối đa 32 vòng lặp).
- **Không gian**: $O(1)$.

---

## 4. Binary Gap

### Đề bài chi tiết
Cho số nguyên dương $n$. Tìm khoảng cách lớn nhất giữa hai bit '1' liên tiếp trong biểu diễn nhị phân của $n$. Nếu không có hai bit '1' nào kề nhau, trả về 0. Khoảng cách là chỉ số chênh lệch.

### Phân tích thuật toán
Duyệt qua các bit của $n$. Lưu lại vị trí (index) của bit '1' trước đó. Khi gặp một bit '1' mới, cập nhật khoảng cách lớn nhất bằng chỉ số hiện tại trừ đi chỉ số trước đó. Cập nhật chỉ số trước đó bằng chỉ số hiện tại.

### Mã nguồn Java
```java
public class Solution {
    public int binaryGap(int n) {
        int lastPos = -1;
        int maxGap = 0;
        for (int i = 0; i < 32; i++) {
            if (((n >> i) & 1) == 1) {
                if (lastPos != -1) {
                    maxGap = Math.max(maxGap, i - lastPos);
                }
                lastPos = i;
            }
        }
        return maxGap;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(1)$ (luôn duyệt 32 bit).
- **Không gian**: $O(1)$.

---

## 5. Sort Integers by The Number of 1 Bits

### Đề bài chi tiết
Cho một mảng các số nguyên `arr`. Hãy sắp xếp mảng theo thứ tự tăng dần dựa trên số lượng bit '1' của từng số. Nếu có nhiều số cùng số bit 1, số nhỏ hơn xếp trước.

### Phân tích thuật toán
Chúng ta có thể đếm số lượng bit '1' của mỗi số bằng thuật toán Brian Kernighan hoặc hàm có sẵn `Integer.bitCount()`. Kết hợp với bộ so sánh custom (`Comparator`) để phân loại, so sánh theo số bit '1' trước, sau đó so sánh giá trị nguyên thủy.

### Mã nguồn Java
```java
import java.util.Arrays;

public class Solution {
    public int[] sortByBits(int[] arr) {
        Integer[] integerArr = Arrays.stream(arr).boxed().toArray(Integer[]::new);
        
        Arrays.sort(integerArr, (a, b) -> {
            int bitCountA = Integer.bitCount(a);
            int bitCountB = Integer.bitCount(b);
            if (bitCountA == bitCountB) {
                return a.compareTo(b);
            }
            return Integer.compare(bitCountA, bitCountB);
        });
        
        return Arrays.stream(integerArr).mapToInt(Integer::intValue).toArray();
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(N \log N)$ để sắp xếp mảng, $N$ là độ dài mảng. Đếm bit mất $O(1)$.
- **Không gian**: $O(N)$ để sử dụng mảng đối tượng `Integer`.

---

## 6. Has Alternating Bits

### Đề bài chi tiết
Cho một số nguyên dương. Hãy kiểm tra xem biểu diễn nhị phân của nó có các bit xen kẽ không (tức là hai bit kề nhau luôn luôn khác nhau, ví dụ: 10101, 101).

### Phân tích thuật toán
Nếu số có các bit xen kẽ (ví dụ `n = 10101`), thì `n >> 1` (01010).
Thực hiện phép XOR: `n ^ (n >> 1)` sẽ ra một chuỗi toàn bit 1 (11111).
Kiểm tra xem chuỗi có toàn bit 1 không bằng cách lấy số đó AND với số đó cộng 1, phải bằng 0: `(x & (x + 1)) == 0`.

### Mã nguồn Java
```java
public class Solution {
    public boolean hasAlternatingBits(int n) {
        int x = n ^ (n >> 1);
        return (x & (x + 1)) == 0;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(1)$.
- **Không gian**: $O(1)$.

---

## 7. Convert a Number to Hexadecimal

### Đề bài chi tiết
Cho một số nguyên, hãy viết thuật toán để chuyển nó sang định dạng hex (cơ số 16). Đối với số âm, sử dụng phương pháp bù 2 (two's complement). Các chữ cái hex cần viết thường. 

### Phân tích thuật toán
Định dạng hex gộp 4 bit nhị phân thành 1 ký tự hex. Do đó, ta có thể liên tục lấy 4 bit cuối bằng cách AND với `15` (`0xf`) để lấy ký tự, sau đó dịch phải số ban đầu 4 bit `>>> 4` (dịch không dấu để xử lý số âm).
Trường hợp đặc biệt `num == 0` trả về `"0"`.

### Mã nguồn Java
```java
public class Solution {
    public String toHex(int num) {
        if (num == 0) return "0";
        char[] map = {'0','1','2','3','4','5','6','7','8','9','a','b','c','d','e','f'};
        StringBuilder sb = new StringBuilder();
        
        while (num != 0) {
            sb.append(map[num & 15]);
            num >>>= 4; // Dịch không dấu
        }
        return sb.reverse().toString();
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(1)$ (tối đa 8 vòng lặp vì 32/4 = 8).
- **Không gian**: $O(1)$.

---

## 8. Prime Number of Set Bits in Binary Representation

### Đề bài chi tiết
Cho hai số nguyên `left` và `right`, trả về số lượng các số nguyên trong đoạn `[left, right]` mà số bit 1 trong biểu diễn nhị phân của nó là một số nguyên tố.

### Phân tích thuật toán
- Lặp qua các số trong đoạn `[left, right]`.
- Tìm số lượng bit 1 của mỗi số (`Integer.bitCount`).
- Kiểm tra xem số lượng bit 1 có phải là số nguyên tố không. Vì số nguyên tối đa là 32 bit, ta chỉ cần kiểm tra các số nguyên tố lên tới 32 là: 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31.
Có thể tối ưu bằng một bitmask cho các số nguyên tố này.

### Mã nguồn Java
```java
public class Solution {
    public int countPrimeSetBits(int left, int right) {
        int count = 0;
        // Các vị trí nguyên tố được set thành 1 (từ 0 đến 31):
        // 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31
        // (1<<2) | (1<<3) | ...
        int primesMask = 665772; 
        
        for (int i = left; i <= right; i++) {
            int bits = Integer.bitCount(i);
            if (((primesMask >> bits) & 1) == 1) {
                count++;
            }
        }
        return count;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(D)$, với $D = right - left + 1$.
- **Không gian**: $O(1)$.

---

## 9. Reverse Bits

### Đề bài chi tiết
Đảo ngược các bit của một số nguyên dương 32 bit không dấu (unsigned).

### Phân tích thuật toán
- Lặp 32 lần (vì đầu vào luôn 32 bit).
- Lấy bit ở tận cùng bên phải của $n$ bằng `n & 1`.
- Đưa bit đó vào vị trí tương ứng trong kết quả bằng cách thêm nó vào `ans` và dịch trái `ans << 1`.
- Dịch phải số n `n >>> 1` (bắt buộc dùng `>>>` vì xử lý số nguyên dương, bù không dấu).

### Mã nguồn Java
```java
public class Solution {
    // you need treat n as an unsigned value
    public int reverseBits(int n) {
        int ans = 0;
        for (int i = 0; i < 32; i++) {
            ans = (ans << 1) | (n & 1);
            n >>>= 1;
        }
        return ans;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(1)$ (luôn lặp 32 lần).
- **Không gian**: $O(1)$.

---

## 10. Find the Difference

### Đề bài chi tiết
Cho hai chuỗi `s` và `t`. Chuỗi `t` được tạo ra bằng cách trộn xáo ngẫu nhiên chuỗi `s` và thêm một ký tự ngẫu nhiên nữa vào vị trí bất kỳ. Tìm ký tự được thêm vào.

### Phân tích thuật toán
Vì chuỗi `t` chứa toàn bộ ký tự của `s` và có thêm 1 ký tự, ta có thể dùng tính chất `A ^ A = 0`.
XOR tất cả các ký tự của cả 2 chuỗi `s` và `t` với nhau. Ký tự nào có 1 bản sao (ký tự mới) sẽ không bị triệt tiêu và là kết quả còn lại cuối cùng.

### Mã nguồn Java
```java
public class Solution {
    public char findTheDifference(String s, String t) {
        char ans = 0;
        for (int i = 0; i < s.length(); i++) {
            ans ^= s.charAt(i);
        }
        for (int i = 0; i < t.length(); i++) {
            ans ^= t.charAt(i);
        }
        return ans;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(N)$, với $N$ là chiều dài của chuỗi `s`.
- **Không gian**: $O(1)$.

---

## 11. Single Number

### Đề bài chi tiết
Cho một mảng số nguyên không trống `nums`, mọi phần tử xuất hiện hai lần trừ một phần tử xuất hiện duy nhất một lần. Hãy tìm phần tử duy nhất đó. Bạn phải triển khai một thuật toán với độ phức tạp thời gian tuyến tính $O(n)$ và chỉ sử dụng không gian bộ nhớ không đổi $O(1)$.

### Phân tích thuật toán
Sử dụng toán tử XOR bit. XOR có các tính chất quan trọng:
1. `a ^ a = 0` (XOR hai số giống nhau sẽ bằng 0)
2. `a ^ 0 = a` (XOR với 0 bằng chính nó)
3. Tính chất giao hoán: `a ^ b ^ c = a ^ c ^ b`

Khi ta XOR tất cả các phần tử trong mảng, tất cả các phần tử xuất hiện hai lần sẽ tự triệt tiêu thành 0. Số duy nhất xuất hiện một lần khi XOR với 0 sẽ là chính nó.

### Mã nguồn Java
```java
public class Solution {
    public int singleNumber(int[] nums) {
        int result = 0;
        for (int num : nums) {
            result ^= num;
        }
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(n)$, với $n$ là số lượng phần tử trong mảng.
- **Không gian**: $O(1)$.

---

## 12. Single Number II

### Đề bài chi tiết
Cho một mảng số nguyên `nums` trong đó mỗi phần tử xuất hiện ba lần trừ một phần tử xuất hiện chính xác một lần. Tìm phần tử duy nhất đó. Bạn phải thiết kế một thuật toán chạy với độ phức tạp thời gian tuyến tính $O(n)$ và sử dụng không gian hằng số $O(1)$.

### Phân tích thuật toán
- **Cách đếm bit**: Đối với mỗi bit từ vị trí 0 đến 31, ta đếm xem có bao nhiêu số trong mảng có bit đó bằng 1. Nếu đếm chia lấy dư cho 3 mà bằng 1, thì bit đó thuộc về số duy nhất. Ta sẽ dùng các bit này để tái tạo lại số kết quả.
- **Cách dùng biến trạng thái**: Ta có thể dùng hai biến `ones` và `twos` để theo dõi các bit xuất hiện 1 lần và 2 lần. Khi một bit xuất hiện lần thứ 3, ta sẽ xóa nó khỏi cả `ones` và `twos`.

### Mã nguồn Java
```java
public class Solution {
    public int singleNumber(int[] nums) {
        int ones = 0, twos = 0;
        for (int num : nums) {
            // Cập nhật 'ones' chỉ khi nó không nằm trong 'twos'
            ones = (ones ^ num) & ~twos;
            // Cập nhật 'twos' chỉ khi nó không nằm trong 'ones'
            twos = (twos ^ num) & ~ones;
        }
        return ones;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(n)$, duyệt mảng 1 lần.
- **Không gian**: $O(1)$.

---

## 13. Single Number III

### Đề bài chi tiết
Cho một mảng số nguyên `nums`, trong đó có đúng hai phần tử xuất hiện duy nhất một lần và tất cả các phần tử khác đều xuất hiện đúng hai lần. Hãy tìm hai phần tử chỉ xuất hiện một lần đó. Kết quả trả về dưới dạng mảng 2 phần tử, thứ tự nào cũng được.

### Phân tích thuật toán
Bước 1: Giống như bài 1, XOR tất cả các phần tử lại. Kết quả thu được sẽ là `xor = a ^ b` (với `a` và `b` là hai số cần tìm). Do `a != b` nên `xor != 0`.
Bước 2: Tìm bit 1 bất kỳ trong `xor` (dễ nhất là bit 1 thấp nhất bằng `xor & -xor`). Bit 1 này cho biết `a` và `b` khác nhau ở vị trí bit đó.
Bước 3: Chia mảng làm 2 nhóm dựa trên vị trí bit này. Một nhóm các số có bit đó là 1, nhóm kia là 0. Sau đó XOR từng nhóm để tìm ra `a` và `b`.

### Mã nguồn Java
```java
public class Solution {
    public int[] singleNumber(int[] nums) {
        int xor = 0;
        for (int num : nums) {
            xor ^= num;
        }
        
        // Tìm bit 1 thấp nhất
        int lowbit = xor & -xor;
        
        int[] result = new int[2];
        for (int num : nums) {
            if ((num & lowbit) == 0) {
                result[0] ^= num;
            } else {
                result[1] ^= num;
            }
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(n)$, duyệt mảng 2 vòng.
- **Không gian**: $O(1)$.

---

## 14. Bitwise AND of Numbers Range

### Đề bài chi tiết
Cho hai số nguyên `left` và `right` đại diện cho một khoảng `[left, right]`, hãy trả về bitwise AND của tất cả các số trong phạm vi này (bao gồm cả hai đầu mút).

### Phân tích thuật toán
Bitwise AND của một tập các số liên tiếp sẽ giữ lại các bit phần tiền tố chung (common prefix) của hai giới hạn `left` và `right`, và biến tất cả các bit phía sau thành 0. 
Vì vậy, ta chỉ cần dịch phải cả `left` và `right` cho đến khi chúng bằng nhau. Số lần dịch phải chính là số lượng bit 0 cần điền vào phía cuối. Sau khi tìm được tiền tố chung, ta dịch trái nó lại số lần tương ứng.

### Mã nguồn Java
```java
public class Solution {
    public int rangeBitwiseAnd(int left, int right) {
        int shift = 0;
        while (left < right) {
            left >>= 1;
            right >>= 1;
            shift++;
        }
        return left << shift;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(1)$ (tối đa 32 vòng lặp).
- **Không gian**: $O(1)$.

---

## 15. Maximum Product of Word Lengths

### Đề bài chi tiết
Cho một mảng các chuỗi `words`. Hãy trả về giá trị lớn nhất của `length(words[i]) * length(words[j])` trong đó hai chuỗi `words[i]` và `words[j]` không có chung ký tự nào. Nếu không tồn tại cặp nào như vậy, trả về 0.

### Phân tích thuật toán
Để kiểm tra xem hai chuỗi có chung ký tự nào không thật nhanh chóng, ta ánh xạ mỗi chuỗi thành một số nguyên 32 bit (bitmask). Có 26 ký tự tiếng Anh, vậy ta dùng 26 bit tương ứng. Bit thứ `k` là 1 nếu chuỗi chứa ký tự đó.
Hai chuỗi `A` và `B` không có chung ký tự khi và chỉ khi `mask(A) & mask(B) == 0`.

### Mã nguồn Java
```java
public class Solution {
    public int maxProduct(String[] words) {
        int n = words.length;
        int[] masks = new int[n];
        for (int i = 0; i < n; i++) {
            for (char c : words[i].toCharArray()) {
                masks[i] |= (1 << (c - 'a'));
            }
        }
        
        int max = 0;
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if ((masks[i] & masks[j]) == 0) {
                    max = Math.max(max, words[i].length() * words[j].length());
                }
            }
        }
        return max;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(N^2 + L)$, với $N$ là số lượng chuỗi và $L$ là tổng số ký tự của tất cả các chuỗi (để tạo bitmask).
- **Không gian**: $O(N)$ để lưu trữ mask.

---

## 16. Subsets

### Đề bài chi tiết
Cho một mảng số nguyên `nums` với các phần tử phân biệt, hãy trả về tất cả các tập con (subsets - power set) có thể có của mảng đó. Tập hợp giải pháp không được chứa các tập con trùng lặp. Bạn có thể trả về các tập con theo thứ tự bất kỳ.

### Phân tích thuật toán
Với một mảng có $n$ phần tử, có đúng $2^n$ tập con. Ta có thể dùng một số nguyên biểu diễn các tập con này. Từ $0$ đến $2^n - 1$, mỗi số (mask) sẽ tương ứng với một tập con. Nếu bit thứ `j` trong mask bằng 1, điều đó có nghĩa là phần tử `nums[j]` được đưa vào tập con hiện tại.

### Mã nguồn Java
```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        int n = nums.length;
        int maxMask = 1 << n; // 2^n
        
        for (int mask = 0; mask < maxMask; mask++) {
            List<Integer> currentSubset = new ArrayList<>();
            for (int j = 0; j < n; j++) {
                if ((mask & (1 << j)) != 0) {
                    currentSubset.add(nums[j]);
                }
            }
            result.add(currentSubset);
        }
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(n \cdot 2^n)$, tạo $2^n$ tập con, mỗi tập cần $O(n)$ bước.
- **Không gian**: $O(n \cdot 2^n)$ để lưu kết quả.

---

## 17. Decode XORed Array

### Đề bài chi tiết
Có một mảng số nguyên dương `arr` (được giấu) bao gồm $n$ phần tử phân biệt. Mảng `encoded` có chiều dài $n-1$, sao cho `encoded[i] = arr[i] XOR arr[i + 1]`. Cho trước mảng `encoded` và số nguyên `first`, đại diện cho phần tử đầu tiên của mảng `arr` (`arr[0]`). Hãy tìm và trả về mảng `arr` ban đầu.

### Phân tích thuật toán
Vì `encoded[i] = arr[i] ^ arr[i+1]`, ta có thể sử dụng tính chất của XOR: Nếu $c = a \oplus b$, thì $b = a \oplus c$.
Áp dụng tính chất này: `arr[i+1] = arr[i] ^ encoded[i]`. Biết phần tử đầu tiên, ta dễ dàng khôi phục từng phần tử kế tiếp của mảng.

### Mã nguồn Java
```java
public class Solution {
    public int[] decode(int[] encoded, int first) {
        int n = encoded.length + 1;
        int[] arr = new int[n];
        arr[0] = first;
        
        for (int i = 0; i < n - 1; i++) {
            arr[i + 1] = arr[i] ^ encoded[i];
        }
        
        return arr;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(n)$, với $n$ là chiều dài của kết quả, duyệt 1 vòng.
- **Không gian**: $O(1)$ (không tính kết quả trả về) hoặc $O(n)$ không gian bộ nhớ mảng cần tạo.

---

## 18. XOR Operation in an Array

### Đề bài chi tiết
Cho hai số nguyên `n` và `start`. Ta có một mảng `nums` chứa $n$ phần tử, trong đó `nums[i] = start + 2 * i` (chỉ số mảng bắt đầu từ $0$). Hãy trả về bitwise XOR của tất cả các phần tử trong `nums`.

### Phân tích thuật toán
Ta chỉ cần khởi tạo biến kết quả `result = 0` và sau đó lặp qua $n$ phần tử, tính giá trị của từng phần tử bằng công thức đã cho và lần lượt thực hiện phép XOR vào `result`.

*(Lưu ý: Có các phương pháp tính thời gian O(1) toán học thuần túy cho thao tác XOR liên tiếp, nhưng ở đây $n$ thông thường nhỏ (đến 1000) nên O(n) là đủ tối ưu và phù hợp nhất để hiểu).*

### Mã nguồn Java
```java
public class Solution {
    public int xorOperation(int n, int start) {
        int result = 0;
        for (int i = 0; i < n; i++) {
            result ^= (start + 2 * i);
        }
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(n)$, thực hiện lặp $n$ lần.
- **Không gian**: $O(1)$.

---

## 19. Hamming Distance

### Đề bài chi tiết
Khoảng cách Hamming giữa hai số nguyên là số lượng các vị trí mà tại đó các bit tương ứng của hai số khác nhau. Cho hai số nguyên `x` và `y`, hãy tính khoảng cách Hamming giữa chúng.

### Phân tích thuật toán
Hai bit khác nhau khi và chỉ khi phép XOR của chúng bằng 1. Ta sẽ lấy phép XOR của `x` và `y`, sau đó đếm số lượng bit '1' có trong kết quả (có thể dùng Brian Kernighan hoặc vòng lặp thông thường).

### Mã nguồn Java
```java
public class Solution {
    public int hammingDistance(int x, int y) {
        int xor = x ^ y;
        int count = 0;
        while (xor != 0) {
            xor &= (xor - 1); // Xóa bit 1 thấp nhất
            count++;
        }
        return count;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(1)$, vì tối đa chỉ 32 bit.
- **Không gian**: $O(1)$.

---

## 20. Power of Four

### Đề bài chi tiết
Cho một số nguyên $n$, hãy viết hàm kiểm tra xem nó có phải là lũy thừa của 4 hay không. Số nguyên dương $n$ là lũy thừa của 4 nếu tồn tại một số nguyên $x$ sao cho $n = 4^x$.

### Phân tích thuật toán
- Số dương là lũy thừa của 4 trước tiên phải là lũy thừa của 2 (nghĩa là `n > 0` và `(n & (n - 1)) == 0`).
- Nếu là lũy thừa của 4, bit '1' duy nhất của nó sẽ nằm ở vị trí chẵn (0-indexed). Ví dụ: $1 = 4^0 = 1_2$ (vị trí 0), $4 = 4^1 = 100_2$ (vị trí 2), $16 = 4^2 = 10000_2$ (vị trí 4).
- Ta có thể kiểm tra xem bit '1' đó có nằm ở vị trí chẵn không bằng cách AND kết quả với một hằng số mặt nạ `0x55555555` (nhị phân: `01010101010101010101010101010101`). Nếu khác 0, thì nó đúng ở vị trí chẵn.

### Mã nguồn Java
```java
public class Solution {
    public boolean isPowerOfFour(int n) {
        return n > 0 && (n & (n - 1)) == 0 && (n & 0x55555555) != 0;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(1)$.
- **Không gian**: $O(1)$.

---

## 21. Counting Bits

### Đề bài chi tiết
Cho một số nguyên $n$, hãy trả về một mảng `ans` có độ dài $n + 1$ sao cho với mỗi $i$ ($0 \le i \le n$), `ans[i]` là số lượng bit '1' trong biểu diễn nhị phân của $i$.

### Phân tích thuật toán
Thuật toán chia để trị / quy hoạch động (Dynamic Programming):
Số lượng bit '1' của số $i$ có thể được tính từ số $i / 2$ (hay $i \gg 1$). 
- Dịch phải 1 bit ($i \gg 1$) tương đương với việc xóa đi bit cuối cùng.
- Do đó, số lượng bit 1 của $i$ sẽ bằng số lượng bit 1 của $i \gg 1$ cộng thêm 1 nếu bit cuối cùng của $i$ là 1 (tức là $i$ lẻ, `i & 1 == 1`), ngược lại cộng thêm 0.
Công thức: `dp[i] = dp[i >> 1] + (i & 1)`.

### Mã nguồn Java
```java
public class Solution {
    public int[] countBits(int n) {
        int[] ans = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            ans[i] = ans[i >> 1] + (i & 1);
        }
        return ans;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(n)$, vì ta chỉ duyệt 1 lần từ 1 đến $n$.
- **Không gian**: $O(n)$ để chứa mảng kết quả (hoặc $O(1)$ nếu không tính mảng trả về).

---

## 22. Set Mismatch

### Đề bài chi tiết
Bạn có một tập hợp các số nguyên `s`, ban đầu chứa tất cả các số từ 1 đến $n$. Thật không may, do lỗi dữ liệu, một trong các số trong tập hợp đã bị nhân bản thành một số khác trong tập hợp, dẫn đến việc lặp lại một số và mất đi một số khác.
Cho một mảng mảng số nguyên `nums` đại diện cho trạng thái dữ liệu hiện tại của tập hợp. Hãy tìm ra số bị lặp lại và số bị thiếu.

### Phân tích thuật toán
Có thể dùng toán học, HashMap, hoặc Thao tác Bit (XOR).
Giải pháp Bitwise:
1. XOR tất cả các phần tử trong `nums` và các số từ $1$ đến $n$. Kết quả sẽ là `xor = duplicate ^ missing`.
2. Tìm bit 1 thấp nhất của `xor` (dùng `lowbit = xor & -xor`).
3. Chia các số trong `nums` và từ $1$ đến $n$ thành hai nhóm dựa trên `lowbit` (nhóm có bit này bằng 0 và bằng 1).
4. XOR các phần tử trong mỗi nhóm để tìm ra `duplicate` và `missing`. Sau đó duyệt lại mảng để xác định số nào là số lặp, số nào là số bị thiếu.

### Mã nguồn Java
```java
public class Solution {
    public int[] findErrorNums(int[] nums) {
        int n = nums.length;
        int xor = 0;
        
        for (int i = 0; i < n; i++) {
            xor ^= nums[i];
            xor ^= (i + 1);
        }
        
        int lowbit = xor & -xor;
        int num1 = 0, num2 = 0;
        
        for (int i = 0; i < n; i++) {
            if ((nums[i] & lowbit) == 0) num1 ^= nums[i];
            else num2 ^= nums[i];
            
            if (((i + 1) & lowbit) == 0) num1 ^= (i + 1);
            else num2 ^= (i + 1);
        }
        
        for (int num : nums) {
            if (num == num1) {
                return new int[]{num1, num2};
            }
        }
        
        return new int[]{num2, num1};
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(n)$.
- **Không gian**: $O(1)$.

---

## 23. Base 7

### Đề bài chi tiết
Cho một số nguyên `num`, trả về một chuỗi chứa dạng biểu diễn cơ số 7 (base-7) của nó.

### Phân tích thuật toán
Mặc dù đây là bài đổi cơ số (toán học), thuật toán chia lấy dư tương tự như việc lấy từng bit bằng phép AND và dịch bit. Thay vì dịch bit (chia 2), ta chia 7:
- Lấy dư `num % 7` để tìm chữ số cơ số 7 cuối cùng.
- Chia nguyên `num / 7` để tìm các chữ số tiếp theo.
- Lặp lại cho đến khi `num` bằng 0. Nếu số âm, ta lưu cờ âm và làm việc với số dương.

### Mã nguồn Java
```java
public class Solution {
    public String convertToBase7(int num) {
        if (num == 0) return "0";
        
        boolean isNegative = num < 0;
        num = Math.abs(num);
        StringBuilder sb = new StringBuilder();
        
        while (num > 0) {
            sb.append(num % 7);
            num /= 7;
        }
        
        if (isNegative) sb.append("-");
        
        return sb.reverse().toString();
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(\log_7(\text{num}))$, số lượng chữ số.
- **Không gian**: $O(\log_7(\text{num}))$ để lưu chuỗi kết quả.

---

## 24. UTF-8 Validation

### Đề bài chi tiết
Cho một mảng số nguyên `data` (mỗi số đại diện cho 1 byte, tức là chỉ tính 8 bit cuối), hãy xác định xem nó có phải là một biểu diễn UTF-8 hợp lệ hay không.
Quy tắc UTF-8 (1 đến 4 byte):
- 1 byte: Bắt đầu bằng `0`
- 2 byte: Byte đầu bắt đầu bằng `110`, 1 byte theo sau bắt đầu bằng `10`
- 3 byte: Byte đầu bắt đầu bằng `1110`, 2 byte theo sau bắt đầu bằng `10`
- 4 byte: Byte đầu bắt đầu bằng `11110`, 3 byte theo sau bắt đầu bằng `10`

### Phân tích thuật toán
Duyệt qua từng số (byte). Sử dụng bit mask để kiểm tra các bit ở phần đầu:
- Dùng các biến theo dõi số lượng byte cần kiểm tra tiếp theo có tiền tố `10`.
- Các hằng số mask: `128` (10000000), `192` (11000000), `224` (11100000), v.v.

### Mã nguồn Java
```java
public class Solution {
    public boolean validUtf8(int[] data) {
        int numberOfBytesToProcess = 0;
        
        for (int i = 0; i < data.length; i++) {
            int binRep = data[i];
            
            if (numberOfBytesToProcess == 0) {
                if ((binRep >> 5) == 0b110) numberOfBytesToProcess = 1;
                else if ((binRep >> 4) == 0b1110) numberOfBytesToProcess = 2;
                else if ((binRep >> 3) == 0b11110) numberOfBytesToProcess = 3;
                else if ((binRep >> 7) != 0) return false;
            } else {
                if ((binRep >> 6) != 0b10) return false;
                numberOfBytesToProcess--;
            }
        }
        
        return numberOfBytesToProcess == 0;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(n)$, duyệt mảng 1 lần.
- **Không gian**: $O(1)$.

---

## 25. Divide Two Integers

### Đề bài chi tiết
Cho hai số nguyên `dividend` (số bị chia) và `divisor` (số chia), thực hiện phép chia nguyên mà không sử dụng phép nhân, phép chia (`/`), hay phép chia lấy dư (`%`). Trả về thương số.
Nếu kết quả tràn số nguyên 32-bit có dấu, hãy trả về giới hạn tương ứng ($2^{31} - 1$ hoặc $-2^{31}$).

### Phân tích thuật toán
Dùng phép trừ và phép dịch trái (Left Shift). 
Thay vì trừ đi số chia từng lần một, ta dịch trái số chia lên (nhân 2, 4, 8...) để có thể trừ đi những lượng lớn nhất có thể.
Ví dụ: $15 / 3$, ta có $3 \cdot 2 = 6, 6 \cdot 2 = 12, 12 \cdot 2 = 24$. Ta lấy $15 - 12 = 3$, phần thương có thêm 4. Số bị chia còn lại 3. Lặp lại tiến trình tương tự.

### Mã nguồn Java
```java
public class Solution {
    public int divide(int dividend, int divisor) {
        if (dividend == Integer.MIN_VALUE && divisor == -1) {
            return Integer.MAX_VALUE; // Xử lý tràn số
        }
        
        boolean isNegative = (dividend < 0) ^ (divisor < 0);
        long absDividend = Math.abs((long) dividend);
        long absDivisor = Math.abs((long) divisor);
        
        int result = 0;
        while (absDividend >= absDivisor) {
            long temp = absDivisor, multiple = 1;
            while (absDividend >= (temp << 1)) {
                temp <<= 1;
                multiple <<= 1;
            }
            absDividend -= temp;
            result += multiple;
        }
        
        return isNegative ? -result : result;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(\log^2(\text{dividend}))$. Vòng lặp ngoài chạy $\log$, vòng trong cũng chạy $\log$.
- **Không gian**: $O(1)$.

---

## 26. Majority Element

### Đề bài chi tiết
Cho một mảng số nguyên `nums` có kích thước $n$, hãy tìm phần tử đa số (majority element).
Phần tử đa số là phần tử xuất hiện nhiều hơn $\lfloor n / 2 \rfloor$ lần. Giả định rằng mảng luôn luôn có một phần tử đa số.

### Phân tích thuật toán
Bên cạnh thuật toán Boyer-Moore Voting (tối ưu nhất), bài này cũng có thể giải bằng Bit Manipulation.
Ta đếm số lượng bit '1' ở mỗi vị trí (từ 0 đến 31) của tất cả các phần tử. Do phần tử đa số chiếm hơn một nửa số lượng, nên nếu phần tử đa số có bit '1' tại vị trí $i$, tổng số lượng bit '1' tại vị trí $i$ của toàn mảng sẽ lớn hơn $n / 2$. Ta khôi phục phần tử đa số từ thông tin này.

### Mã nguồn Java
```java
public class Solution {
    public int majorityElement(int[] nums) {
        int majority = 0;
        int n = nums.length;
        
        for (int i = 0; i < 32; i++) {
            int bitCount = 0;
            for (int num : nums) {
                if (((num >> i) & 1) == 1) {
                    bitCount++;
                }
            }
            if (bitCount > n / 2) {
                majority |= (1 << i);
            }
        }
        
        return majority;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(n \cdot 32) = O(n)$.
- **Không gian**: $O(1)$.

---

## 27. Integer Replacement

### Đề bài chi tiết
Cho một số nguyên dương $n$, bạn có thể thực hiện các thao tác sau:
- Nếu $n$ chẵn, thay thế $n$ bằng $n / 2$.
- Nếu $n$ lẻ, thay thế $n$ bằng $n + 1$ hoặc $n - 1$.
Hãy trả về số bước tối thiểu cần thiết để biến $n$ thành 1.

### Phân tích thuật toán
Sử dụng Greedy và Bit Manipulation để quyết định khi gặp số lẻ.
Ta ưu tiên tạo ra càng nhiều số 0 ở cuối cùng càng tốt để các thao tác chia 2 liên tiếp diễn ra nhiều nhất.
Kiểm tra hai bit cuối cùng (`n & 3`):
- Nếu bằng `01` (VD: ...01), việc trừ đi 1 (`n - 1`) sẽ tạo ra `...00`, ta có thể chia 2 hai lần.
- Nếu bằng `11` (VD: ...11), việc cộng 1 (`n + 1`) sẽ tạo ra `...00`, trừ trường hợp đặc biệt $n = 3$, ta nên trừ 1 thành 2 rồi chia thành 1 (tốn 2 bước, nhanh hơn cộng).

### Mã nguồn Java
```java
public class Solution {
    public int integerReplacement(int n) {
        long num = n; // Dùng long để tránh tràn số khi n = 2^31 - 1
        int count = 0;
        
        while (num != 1) {
            if ((num & 1) == 0) {
                num >>= 1;
            } else {
                if (num == 3 || (num & 3) == 1) {
                    num--;
                } else {
                    num++;
                }
            }
            count++;
        }
        
        return count;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(\log n)$.
- **Không gian**: $O(1)$.

---

## 28. Missing Number

### Đề bài chi tiết
Cho một mảng `nums` chứa $n$ số phân biệt nằm trong khoảng `[0, n]`, trả về số duy nhất bị thiếu trong phạm vi đó.

### Phân tích thuật toán
Tương tự bài Single Number. Ta sử dụng phép XOR.
Biết rằng khoảng giá trị là từ `0` đến `n` (gồm $n+1$ số), còn mảng chỉ chứa $n$ số.
Ta lấy XOR tất cả các số từ `0` đến `n`, đồng thời XOR tất cả các số có trong mảng. Các số xuất hiện cả ở vòng lặp chỉ số và trong mảng sẽ bị triệt tiêu thành 0. Số duy nhất còn sót lại chính là số bị thiếu.

### Mã nguồn Java
```java
public class Solution {
    public int missingNumber(int[] nums) {
        int missing = nums.length; // Khởi tạo bằng n (phần tử cuối cùng)
        for (int i = 0; i < nums.length; i++) {
            missing ^= i ^ nums[i]; // XOR chỉ số và giá trị
        }
        return missing;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(n)$, duyệt mảng 1 lần.
- **Không gian**: $O(1)$.

---

## 29. Letter Case Permutation

### Đề bài chi tiết
Cho một chuỗi `s`, bạn có thể đổi từng ký tự chữ cái thành chữ hoa hoặc chữ thường để tạo ra chuỗi mới. Trả về tất cả các chuỗi có thể được tạo ra.

### Phân tích thuật toán
Chữ cái hoa và thường trong bảng mã ASCII chỉ khác nhau đúng 1 bit ở vị trí thứ 5 (giá trị 32).
Ví dụ: 'a' (97 = 01100001) và 'A' (65 = 01000001).
Sử dụng toán tử bitwise XOR với không gian tìm kiếm quay lui (Backtracking). Để chuyển đổi trạng thái giữa hoa và thường, chỉ cần thực hiện `c ^ 32` (tức là `c ^ (1 << 5)`).

### Mã nguồn Java
```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public List<String> letterCasePermutation(String s) {
        List<String> ans = new ArrayList<>();
        backtrack(s.toCharArray(), 0, ans);
        return ans;
    }
    
    private void backtrack(char[] chars, int i, List<String> ans) {
        if (i == chars.length) {
            ans.add(new String(chars));
            return;
        }
        
        // Luôn luôn duyệt nhánh không chuyển đổi chữ cái (hoặc là số)
        backtrack(chars, i + 1, ans);
        
        // Nếu là chữ cái, tạo thêm nhánh có chuyển đổi hoa/thường
        if (Character.isLetter(chars[i])) {
            chars[i] ^= (1 << 5); // Toggle hoa thường
            backtrack(chars, i + 1, ans);
            // backtrack xong không cần trả lại vì ở nhánh tiếp theo không dùng chung mảng sao chép,
            // nhưng vì ta đệ quy tuần tự, nên nếu khôi phục thì code sẽ an toàn. Ở đây không bắt buộc.
            chars[i] ^= (1 << 5); // Khôi phục (tùy chọn)
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(n \cdot 2^L)$, với $n$ là chiều dài chuỗi, $L$ là số lượng chữ cái.
- **Không gian**: $O(n)$ cho chiều sâu đệ quy (không tính kết quả trả về).

---

## 30. Check if a String Contains All Binary Codes of Size K

### Đề bài chi tiết
Cho một chuỗi nhị phân `s` và một số nguyên `k`. Hãy trả về `true` nếu tất cả các chuỗi nhị phân độ dài `k` đều là chuỗi con (substring) của `s`. Ngược lại trả về `false`.

### Phân tích thuật toán
Có tổng cộng $2^k$ chuỗi nhị phân có kích thước $k$. Để kiểm tra xem chuỗi có chứa tất cả hay không, ta trượt cửa sổ cỡ $k$ qua chuỗi `s` và băm các mã.
Sử dụng Rolling Hash (Bitwise Mask) để chuyển đổi mỗi substring cỡ $k$ thành một số nguyên:
- Tại mỗi bước, ta lấy số ở bước trước dịch trái 1 bit (`val << 1`), kết hợp ký tự mới nhất bằng phép toán OR (`| (s.charAt(i) - '0')`), sau đó bỏ đi bit ngoài cùng bên trái (`& mask`) với `mask = (1 << k) - 1`.
- Lưu các giá trị nhận được vào một Set, hoặc một mảng `boolean[]`. Nếu kích thước đạt đủ $2^k$ thì trả về true.

### Mã nguồn Java
```java
public class Solution {
    public boolean hasAllCodes(String s, int k) {
        int need = 1 << k; // 2^k
        boolean[] got = new boolean[need];
        int allOnes = need - 1; // Mask
        int hashVal = 0;
        
        for (int i = 0; i < s.length(); i++) {
            // Shift trái, OR với bit mới, và lấy dư bằng cách AND mask
            hashVal = ((hashVal << 1) & allOnes) | (s.charAt(i) - '0');
            
            // Cửa sổ trượt đã đủ kích thước k chưa
            if (i >= k - 1 && !got[hashVal]) {
                got[hashVal] = true;
                need--;
                if (need == 0) {
                    return true;
                }
            }
        }
        
        return false;
    }
}
```

### Độ phức tạp
- **Thời gian**: $O(n)$, với $n$ là chiều dài chuỗi `s`.
- **Không gian**: $O(2^k)$, kích thước mảng đánh dấu `got`.
