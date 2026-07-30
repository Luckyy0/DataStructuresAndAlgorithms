# 05 - Bài tập Các mẫu Sắp xếp Nâng cao (Advanced Sorting Patterns Problems)

Tài liệu này cung cấp danh sách $30$ bài tập tập trung vào Cyclic Sort, Custom Comparator và Minimum Swaps to Sort. $10$ bài đầu tiên được cung cấp đầy đủ đề bài, phân tích, mã nguồn Java và phân tích độ phức tạp. Các bài từ $11$ đến $30$ được tóm tắt sơ lược.

## 1. Missing Number
**Đề bài chi tiết:**
Cho mảng `nums` chứa $n$ số nguyên phân biệt trong khoảng từ $0$ đến $n$. Trả về con số duy nhất bị thiếu trong dải số đó. Yêu cầu độ phức tạp thời gian $O(N)$ và không gian $O(1)$.

**Phân tích thuật toán:**
Dữ liệu nằm trong khoảng $0..n$. Ta có thể sử dụng Cyclic Sort, đặt mỗi số `v = nums[i]` vào vị trí `v` tương ứng. Những số nằm ngoài khoảng mảng hoặc có giá trị lớn hơn $n-1$ thì bỏ qua trong bước hoán vị. Sau đó duyệt một lần nữa, index nào có giá trị sai lệch chính là số bị thiếu.

**Mã nguồn Java:**
```java
public class MissingNumber {
    public int missingNumber(int[] nums) {
        int i = 0, n = nums.length;
        while (i < n) {
            int j = nums[i];
            if (nums[i] < n && nums[i] != nums[j]) {
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;
            } else {
                i++;
            }
        }
        for (i = 0; i < n; i++) {
            if (nums[i] != i) {
                return i;
            }
        }
        return n;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$ vì mỗi phần tử tối đa bị swap 1 lần.
- Space Complexity: $O(1)$ do thuật toán in-place.

---

## 2. Find All Duplicates in an Array
**Đề bài chi tiết:**
Cho mảng số nguyên `nums` có kích thước $n$, trong đó các số từ $1$ đến $n$. Mỗi số có thể xuất hiện một lần hoặc hai lần. Tìm một mảng chứa tất cả các số xuất hiện hai lần. Bạn phải viết thuật toán chạy trong $O(N)$ time và chỉ dùng $O(1)$ extra space.

**Phân tích thuật toán:**
Dùng Cyclic Sort, đưa `nums[i]` về vị trí `nums[i] - 1`. Trong khi swap, nếu số cần swap tới vị trí mới mà vị trí đó đã chứa giá trị đúng bằng nó thì ta tăng `i++`. Lượt duyệt cuối, nếu `nums[i] != i + 1` thì `nums[i]` là duplicate.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class FindAllDuplicates {
    public List<Integer> findDuplicates(int[] nums) {
        int i = 0;
        while (i < nums.length) {
            int correctIdx = nums[i] - 1;
            if (nums[i] != nums[correctIdx]) {
                int temp = nums[i];
                nums[i] = nums[correctIdx];
                nums[correctIdx] = temp;
            } else {
                i++;
            }
        }
        List<Integer> duplicates = new ArrayList<>();
        for (i = 0; i < nums.length; i++) {
            if (nums[i] != i + 1) {
                duplicates.add(nums[i]);
            }
        }
        return duplicates;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$.
- Space Complexity: $O(1)$ (bỏ qua list trả về).

---

## 3. First Missing Positive
**Đề bài chi tiết:**
Cho mảng số nguyên không được sắp xếp `nums`. Tìm số nguyên dương nhỏ nhất bị thiếu. Yêu cầu thuật toán chạy $O(N)$ time và $O(1)$ auxiliary space.

**Phân tích thuật toán:**
Số dương nhỏ nhất bị thiếu nằm trong khoảng $1$ đến $n+1$. Sử dụng Cyclic Sort: nếu `nums[i]` nằm trong khoảng $[1, n]$ và chưa đứng đúng vị trí `nums[i] - 1`, ta sẽ swap nó về vị trí đó. Cuối cùng, số bị thiếu chính là `index + 1` đầu tiên không thoả mãn `nums[index] == index + 1`.

**Mã nguồn Java:**
```java
public class FirstMissingPositive {
    public int firstMissingPositive(int[] nums) {
        int i = 0, n = nums.length;
        while (i < n) {
            int j = nums[i] - 1;
            // Chỉ swap các số từ 1..n
            if (nums[i] > 0 && nums[i] <= n && nums[i] != nums[j]) {
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;
            } else {
                i++;
            }
        }
        for (i = 0; i < n; i++) {
            if (nums[i] != i + 1) {
                return i + 1;
            }
        }
        return n + 1;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$.
- Space Complexity: $O(1)$.

---

## 4. Set Mismatch
**Đề bài chi tiết:**
Bạn có một tập số từ $1$ đến $n$. Bị lỗi mất một số được thay bằng một số lặp lại khác. Trả về một mảng chứa [số bị lặp, số bị thiếu].

**Phân tích thuật toán:**
Tiếp tục sử dụng Cyclic Sort, đặt phần tử về `index = value - 1`. Số đứng sai chỗ cuối cùng chính là số lặp lại, và vị trí đó chỉ ra số bị thiếu (index + 1).

**Mã nguồn Java:**
```java
public class SetMismatch {
    public int[] findErrorNums(int[] nums) {
        int i = 0;
        while (i < nums.length) {
            int j = nums[i] - 1;
            if (nums[i] != nums[j]) {
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;
            } else {
                i++;
            }
        }
        for (i = 0; i < nums.length; i++) {
            if (nums[i] != i + 1) {
                return new int[]{nums[i], i + 1};
            }
        }
        return new int[]{-1, -1};
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$.
- Space Complexity: $O(1)$.

---

## 5. Find All Numbers Disappeared in an Array
**Đề bài chi tiết:**
Cho mảng `nums` gồm $n$ số nguyên, trong đó `nums[i]` nằm trong khoảng $[1, n]$. Tìm và trả về danh sách tất cả các số biến mất không xuất hiện. In-place, time $O(N)$.

**Phân tích thuật toán:**
Cyclic sort hoàn toàn có thể tìm ra tất cả các số không xuất hiện bằng cách quét qua một lần nữa và lấy những `index + 1` tại các vị trí `nums[index] != index + 1`.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class DisappearedNumbers {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        int i = 0;
        while (i < nums.length) {
            int j = nums[i] - 1;
            if (nums[i] != nums[j]) {
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;
            } else {
                i++;
            }
        }
        List<Integer> missing = new ArrayList<>();
        for (i = 0; i < nums.length; i++) {
            if (nums[i] != i + 1) {
                missing.add(i + 1);
            }
        }
        return missing;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$.
- Space Complexity: $O(1)$ (bỏ qua ArrayList đầu ra).

---

## 6. Largest Number
**Đề bài chi tiết:**
Cho một mảng số nguyên dương `nums`, hãy sắp xếp mảng lại sao cho các phần tử ghép với nhau tạo thành một số lớn nhất. Giá trị trả về phải là chuỗi (String) do số có thể vượt qua giới hạn của Integer.

**Phân tích thuật toán:**
Chuyển đổi số nguyên thành chuỗi. Thay vì so sánh chuỗi theo chiều độ lớn thông thường, ta so sánh chuỗi kết quả `s1 + s2` và `s2 + s1`. Điều này giúp tìm ra order chính xác cho Largest Number (Custom Comparator).

**Mã nguồn Java:**
```java
import java.util.Arrays;

public class LargestNumber {
    public String largestNumber(int[] nums) {
        String[] s_num = new String[nums.length];
        for(int i = 0; i < nums.length; i++) {
            s_num[i] = String.valueOf(nums[i]);
        }
        
        Arrays.sort(s_num, (a, b) -> {
            String order1 = a + b;
            String order2 = b + a;
            return order2.compareTo(order1); // Đảo ngược so sánh để thành giảm dần
        });
        
        if (s_num[0].equals("0")) {
            return "0";
        }
        
        StringBuilder sb = new StringBuilder();
        for (String s : s_num) {
            sb.append(s);
        }
        return sb.toString();
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \log N \times K)$, với $K$ là độ dài cực đại của chuỗi ghép.
- Space Complexity: $O(N)$ để giữ mảng String.

---

## 7. Reorder Data in Log Files
**Đề bài chi tiết:**
Bạn được cấp mảng các `logs`. Các logs được chia thành 2 loại: Letter-logs (chuỗi từ khóa) và Digit-logs (chuỗi số). Letter-logs xếp trước, phân loại theo thứ tự từ điển, nếu thân giống nhau thì xét identifier. Digit-logs giữ nguyên thứ tự ban đầu tương đối.

**Phân tích thuật toán:**
Sử dụng Custom Comparator trong `Arrays.sort()`. Phân chia logs, xác định là letter log hay digit log. Letter logs so sánh nội dung, sau đó id. Digit logs trả về 0 (nhằm kích hoạt Stable sort của `Arrays.sort()` giữ nguyên order). 

**Mã nguồn Java:**
```java
import java.util.Arrays;

public class ReorderLogFiles {
    public String[] reorderLogFiles(String[] logs) {
        Arrays.sort(logs, (log1, log2) -> {
            String[] split1 = log1.split(" ", 2);
            String[] split2 = log2.split(" ", 2);
            
            boolean isDigit1 = Character.isDigit(split1[1].charAt(0));
            boolean isDigit2 = Character.isDigit(split2[1].charAt(0));
            
            if (!isDigit1 && !isDigit2) {
                // Cả hai là letter logs
                int cmp = split1[1].compareTo(split2[1]);
                if (cmp != 0) return cmp;
                return split1[0].compareTo(split2[0]);
            }
            
            if (!isDigit1 && isDigit2) return -1; // letter log lên trước
            if (isDigit1 && !isDigit2) return 1;
            return 0; // cả hai là digit log, giữ nguyên bằng stable sort
        });
        return logs;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \log N \times M)$ với $M$ là chiều dài log cực đại.
- Space Complexity: $O(\log N)$ (stack frame của Timsort) hoặc $O(N)$ do tạo mảng chuỗi split.

---

## 8. Minimum Swaps to Sort
**Đề bài chi tiết:**
Tìm số thao tác hoán đổi vị trí (swap) tối thiểu để biến một mảng số nguyên không trùng lặp thành mảng được sắp xếp tăng dần.

**Phân tích thuật toán:**
Đây là một bài toán cổ điển Graph-based. Xem mỗi phần tử sai chỗ thuộc một cycle (chu trình đồ thị) `nums[i] -> target_index`. Với một cycle độ dài $L$, số swap cần thiết là $L - 1$. Số tổng swap bằng Tổng của các $(L_k - 1)$. 

**Mã nguồn Java:**
```java
import java.util.Arrays;
import java.util.HashMap;
import java.util.Map;

public class MinimumSwaps {
    public int minSwaps(int[] nums) {
        int n = nums.length;
        int[] temp = Arrays.copyOf(nums, n);
        Arrays.sort(temp);
        
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < n; i++) {
            map.put(temp[i], i); // Ghi nhận vị trí đích
        }
        
        boolean[] visited = new boolean[n];
        int ans = 0;
        
        for (int i = 0; i < n; i++) {
            if (visited[i] || map.get(nums[i]) == i) {
                continue;
            }
            
            int cycleSize = 0;
            int j = i;
            while (!visited[j]) {
                visited[j] = true;
                j = map.get(nums[j]);
                cycleSize++;
            }
            if (cycleSize > 0) {
                ans += (cycleSize - 1);
            }
        }
        return ans;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \log N)$ do thao tác copy và Sort.
- Space Complexity: $O(N)$ cho HashMap và visited array.

---

## 9. Custom Sort String
**Đề bài chi tiết:**
Cho một chuỗi định nghĩa thứ tự `order` chứa các ký tự duy nhất, và một chuỗi đích `s`. Sắp xếp ký tự trong `s` sao cho nó khớp với trật tự ưu tiên từ điển của `order`.

**Phân tích thuật toán:**
Đây là một bài có thể giải bằng Counting thay vì sort truyền thống (O(N)), tuy nhiên bài toán cốt lõi là Custom Sort. Dùng một mảng trọng số để gán thứ tự xuất hiện của ký tự trong `order`, sau đó gọi Custom Comparator trên `s`.

**Mã nguồn Java (Dùng Counting $O(N)$ hoặc Character array with Comparator):**
```java
public class CustomSortString {
    public String customSortString(String order, String s) {
        int[] count = new int[26];
        for (char c : s.toCharArray()) {
            count[c - 'a']++;
        }
        
        StringBuilder sb = new StringBuilder();
        // Append characters matching the order string
        for (char c : order.toCharArray()) {
            while (count[c - 'a'] > 0) {
                sb.append(c);
                count[c - 'a']--;
            }
        }
        // Append remaining characters not in order
        for (char c = 'a'; c <= 'z'; c++) {
            while (count[c - 'a'] > 0) {
                sb.append(c);
                count[c - 'a']--;
            }
        }
        return sb.toString();
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(|order| + |s|)$.
- Space Complexity: $O(1)$ đối với bảng counting (size 26) + không gian $O(N)$ string builder.

---

## 10. Sort Integers by The Power Value
**Đề bài chi tiết:**
Sức mạnh (Power) của số nguyên `x` được định nghĩa là số bước để đưa nó về $1$ bằng cách áp dụng `x = x / 2` (nếu chẵn) hoặc `x = 3x + 1` (nếu lẻ). Cho `lo` và `hi`, sắp xếp chúng dựa trên Power Value, nếu bằng nhau thì theo giá trị tự nhiên. Tìm phần tử thứ $K$.

**Phân tích thuật toán:**
Dùng mảng/danh sách để chứa đối tượng hoặc mảng số. Triển khai Custom Comparator. Tính power function đằng sau, và lưu trữ cache (HashMap) để tối ưu thời gian.

**Mã nguồn Java:**
```java
import java.util.Arrays;
import java.util.HashMap;
import java.util.Map;

public class SortByPower {
    Map<Integer, Integer> memo = new HashMap<>();

    public int getKth(int lo, int hi, int k) {
        Integer[] nums = new Integer[hi - lo + 1];
        for (int i = 0; i < nums.length; i++) {
            nums[i] = lo + i;
        }

        Arrays.sort(nums, (a, b) -> {
            int powerA = getPower(a);
            int powerB = getPower(b);
            if (powerA != powerB) {
                return Integer.compare(powerA, powerB);
            }
            return Integer.compare(a, b);
        });

        return nums[k - 1];
    }

    private int getPower(int x) {
        if (x == 1) return 0;
        if (memo.containsKey(x)) return memo.get(x);
        
        int steps = 1 + (x % 2 == 0 ? getPower(x / 2) : getPower(3 * x + 1));
        memo.put(x, steps);
        return steps;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \log N)$ (chưa tính chi phí memo cache cho Collatz conjecture).
- Space Complexity: $O(N)$ memo cache và mảng lưu trữ.

---

## 11. Sort Array by Parity
**Đề bài chi tiết:**
Cho mảng số nguyên `nums`. Di chuyển tất cả các số chẵn lên đầu mảng và các số lẻ về cuối mảng. Trả về mảng bất kỳ thỏa mãn điều kiện này.

**Phân tích thuật toán:**
Sử dụng kỹ thuật 2 pointers (hai con trỏ). Một con trỏ `i` bắt đầu từ đầu mảng, con trỏ `j` bắt đầu từ cuối mảng. Nếu `nums[i]` lẻ và `nums[j]` chẵn, ta hoán đổi chúng. Nếu `nums[i]` chẵn, tăng `i`. Nếu `nums[j]` lẻ, giảm `j`.

**Mã nguồn Java:**
```java
public class SortArrayByParity {
    public int[] sortArrayByParity(int[] nums) {
        int i = 0, j = nums.length - 1;
        while (i < j) {
            if (nums[i] % 2 > nums[j] % 2) {
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;
            }
            if (nums[i] % 2 == 0) i++;
            if (nums[j] % 2 == 1) j--;
        }
        return nums;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$.
- Space Complexity: $O(1)$ in-place.

---

## 12. Sort Array by Parity II
**Đề bài chi tiết:**
Cho mảng số nguyên `nums` có độ dài chẵn, một nửa là số chẵn và nửa còn lại là số lẻ. Hãy sắp xếp mảng sao cho nếu `i` chẵn thì `nums[i]` chẵn, và nếu `i` lẻ thì `nums[i]` lẻ.

**Phân tích thuật toán:**
Dùng 2 pointers, `even` trỏ tới vị trí chẵn (bắt đầu từ 0) và `odd` trỏ tới vị trí lẻ (bắt đầu từ 1). Duyệt `even` qua mảng, nếu `nums[even]` lẻ, ta tìm một số chẵn đang nằm ở vị trí `odd` (duyệt `odd` lên và tăng dần +2) để swap.

**Mã nguồn Java:**
```java
public class SortArrayByParityII {
    public int[] sortArrayByParityII(int[] nums) {
        int i = 0, j = 1, n = nums.length;
        while (i < n && j < n) {
            while (i < n && nums[i] % 2 == 0) i += 2;
            while (j < n && nums[j] % 2 == 1) j += 2;
            if (i < n && j < n) {
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;
            }
        }
        return nums;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$.
- Space Complexity: $O(1)$.

---

## 13. Sort Colors (Dutch National Flag)
**Đề bài chi tiết:**
Cho mảng `nums` chứa các số `0`, `1` và `2` đại diện cho màu đỏ, trắng và xanh. Hãy sắp xếp mảng in-place sao cho các phần tử cùng màu nằm cạnh nhau theo thứ tự đỏ, trắng, xanh. Không được sử dụng hàm sort có sẵn của thư viện.

**Phân tích thuật toán:**
Ứng dụng thuật toán Dutch National Flag của Dijkstra với $3$ pointers: `low`, `mid`, và `high`. `mid` duyệt qua mảng. Nếu `nums[mid] == 0`, swap `nums[low]` và `nums[mid]`, tăng `low` và `mid`. Nếu `nums[mid] == 1`, tăng `mid`. Nếu `nums[mid] == 2`, swap `nums[mid]` và `nums[high]`, giảm `high`.

**Mã nguồn Java:**
```java
public class SortColors {
    public void sortColors(int[] nums) {
        int low = 0, mid = 0, high = nums.length - 1;
        while (mid <= high) {
            if (nums[mid] == 0) {
                int temp = nums[low];
                nums[low] = nums[mid];
                nums[mid] = temp;
                low++;
                mid++;
            } else if (nums[mid] == 1) {
                mid++;
            } else {
                int temp = nums[mid];
                nums[mid] = nums[high];
                nums[high] = temp;
                high--;
            }
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$ quét mảng 1 lần.
- Space Complexity: $O(1)$.

---

## 14. H-Index
**Đề bài chi tiết:**
Cho mảng `citations` trong đó `citations[i]` là số lượng trích dẫn của bài báo thứ `i`. Tính H-Index của nhà nghiên cứu, tức là giá trị `h` lớn nhất sao cho có ít nhất `h` bài báo có số trích dẫn $\ge h$.

**Phân tích thuật toán:**
Dùng Counting Sort (hoặc Bucket Sort). Tạo một mảng `count` có kích thước $N+1$. Đếm số lượng bài báo cho mỗi mức trích dẫn, nếu số trích dẫn lớn hơn $N$, tính vào `count[N]`. Sau đó, duyệt ngược mảng đếm, tích lũy số lượng bài báo cho đến khi tổng này $\ge i$, lúc đó `i` chính là H-Index.

**Mã nguồn Java:**
```java
public class HIndex {
    public int hIndex(int[] citations) {
        int n = citations.length;
        int[] count = new int[n + 1];
        for (int c : citations) {
            if (c >= n) {
                count[n]++;
            } else {
                count[c]++;
            }
        }
        int total = 0;
        for (int i = n; i >= 0; i--) {
            total += count[i];
            if (total >= i) {
                return i;
            }
        }
        return 0;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$.
- Space Complexity: $O(N)$.

---

## 15. Maximum Gap
**Đề bài chi tiết:**
Cho mảng số nguyên không âm chưa sắp xếp `nums`. Trả về khoảng cách lớn nhất giữa hai phần tử liên tiếp trong phiên bản đã được sắp xếp của mảng. Yêu cầu chạy trong thời gian $O(N)$ và không gian $O(N)$.

**Phân tích thuật toán:**
Dùng Pigeonhole Principle (Nguyên lý chuồng bồ câu) / Bucket Sort. Tính `min` và `max` của mảng. Khoảng cách gap trung bình là $gap = \lceil (max - min) / (n - 1) \rceil$. Chia các phần tử vào các buckets dựa trên $gap$. Khoảng cách lớn nhất chắc chắn không nằm trong một bucket (vì kích thước bucket $< gap$), mà là khoảng cách giữa giá trị lớn nhất của bucket trước và giá trị nhỏ nhất của bucket sau.

**Mã nguồn Java:**
```java
import java.util.Arrays;

public class MaximumGap {
    public int maximumGap(int[] nums) {
        if (nums == null || nums.length < 2) return 0;
        int n = nums.length;
        int minVal = nums[0], maxVal = nums[0];
        for (int i = 1; i < n; i++) {
            minVal = Math.min(minVal, nums[i]);
            maxVal = Math.max(maxVal, nums[i]);
        }
        if (minVal == maxVal) return 0;
        
        int bucketSize = Math.max(1, (maxVal - minVal) / (n - 1));
        int bucketCount = (maxVal - minVal) / bucketSize + 1;
        
        int[] bucketMin = new int[bucketCount];
        int[] bucketMax = new int[bucketCount];
        Arrays.fill(bucketMin, Integer.MAX_VALUE);
        Arrays.fill(bucketMax, Integer.MIN_VALUE);
        
        for (int num : nums) {
            int idx = (num - minVal) / bucketSize;
            bucketMin[idx] = Math.min(bucketMin[idx], num);
            bucketMax[idx] = Math.max(bucketMax[idx], num);
        }
        
        int maxGap = 0, prevMax = minVal;
        for (int i = 0; i < bucketCount; i++) {
            if (bucketMin[i] == Integer.MAX_VALUE) continue; // Bucket rỗng
            maxGap = Math.max(maxGap, bucketMin[i] - prevMax);
            prevMax = bucketMax[i];
        }
        return maxGap;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$.
- Space Complexity: $O(N)$.

---

## 16. Find the Duplicate Number
**Đề bài chi tiết:**
Cho mảng `nums` chứa $n+1$ số nguyên trong khoảng từ $1$ đến $n$. Biết rằng chỉ có đúng một số bị lặp lại, hãy tìm số đó. Không được sửa đổi mảng ban đầu và chỉ dùng bộ nhớ $O(1)$.

**Phân tích thuật toán:**
Đây có thể coi là bài toán tìm chu trình trong LinkedList (Floyd's Cycle Detection). Coi mỗi phần tử `nums[i]` là con trỏ trỏ tới vị trí `nums[i]`. Vì có phần tử lặp lại, nhiều con trỏ sẽ trỏ về cùng một vị trí, tạo ra chu trình. Dùng 2 con trỏ `slow` và `fast` để tìm điểm giao, sau đó tìm điểm bắt đầu của chu trình (đó chính là số lặp).

**Mã nguồn Java:**
```java
public class FindDuplicate {
    public int findDuplicate(int[] nums) {
        int slow = nums[0];
        int fast = nums[0];
        // Tìm điểm giao nhau trong chu trình
        do {
            slow = nums[slow];
            fast = nums[nums[fast]];
        } while (slow != fast);
        
        // Tìm điểm bắt đầu của chu trình (số bị lặp)
        slow = nums[0];
        while (slow != fast) {
            slow = nums[slow];
            fast = nums[fast];
        }
        return slow;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$.
- Space Complexity: $O(1)$.

---

## 17. Wiggle Sort
**Đề bài chi tiết:**
Cho mảng `nums`, hãy sắp xếp lại in-place theo dạng sóng: `nums[0] <= nums[1] >= nums[2] <= nums[3]...`.

**Phân tích thuật toán:**
Thay vì sắp xếp mảng tốn $O(N \log N)$, ta có thể duyệt qua mảng và so sánh các cặp liền kề. Tại index lẻ `i`, yêu cầu `nums[i] >= nums[i-1]`. Nếu không thỏa mãn, hoán đổi. Tại index chẵn `i`, yêu cầu `nums[i] <= nums[i-1]`. Nếu không thỏa mãn, hoán đổi. Thuật toán này có tên là One-pass swap.

**Mã nguồn Java:**
```java
public class WiggleSort {
    public void wiggleSort(int[] nums) {
        for (int i = 1; i < nums.length; i++) {
            // i lẻ: nums[i] phải >= nums[i-1]
            // i chẵn: nums[i] phải <= nums[i-1]
            if ((i % 2 == 1 && nums[i] < nums[i - 1]) ||
                (i % 2 == 0 && nums[i] > nums[i - 1])) {
                int temp = nums[i];
                nums[i] = nums[i - 1];
                nums[i - 1] = temp;
            }
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$.
- Space Complexity: $O(1)$.

---

## 18. Wiggle Sort II
**Đề bài chi tiết:**
Cho mảng `nums`, sắp xếp theo dạng sóng với bất đẳng thức nghiêm ngặt: `nums[0] < nums[1] > nums[2] < nums[3]...`.

**Phân tích thuật toán:**
Sắp xếp mảng, sau đó phân phối các phần tử nhỏ hơn vào vị trí chẵn, và các phần tử lớn hơn vào vị trí lẻ. Để tránh các số bằng median bị xếp cạnh nhau (vi phạm tính nghiêm ngặt `<` hoặc `>`), ta phải xếp đan xen từ cuối lên đầu của nửa bé và nửa lớn. (Thuật toán $O(N)$ bằng Median-finding phức tạp hơn nhiều nên $O(N \log N)$ bằng sort là chấp nhận được cho đa số ứng dụng).

**Mã nguồn Java:**
```java
import java.util.Arrays;

public class WiggleSortII {
    public void wiggleSort(int[] nums) {
        int n = nums.length;
        int[] copy = Arrays.copyOf(nums, n);
        Arrays.sort(copy);
        
        int i = (n - 1) / 2; // Con trỏ cho nửa bé
        int j = n - 1;       // Con trỏ cho nửa lớn
        
        for (int k = 0; k < n; k++) {
            if (k % 2 == 0) {
                nums[k] = copy[i--];
            } else {
                nums[k] = copy[j--];
            }
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \log N)$ do sắp xếp mảng.
- Space Complexity: $O(N)$ cho mảng copy.

---

## 19. Pancake Sorting
**Đề bài chi tiết:**
Cho mảng `arr`, hãy sắp xếp mảng bằng cách thực hiện một loạt các thao tác đảo ngược mảng con từ đầu đến index `k` (gọi là pancake flip). Trả về danh sách các giá trị `k` tương ứng với mỗi lần lật mảng.

**Phân tích thuật toán:**
Tương tự như Selection Sort. Tìm vị trí của phần tử lớn nhất hiện tại chưa được sắp xếp. Giả sử phần tử lớn nhất nằm ở index `i`. Trước tiên, flip từ `0` đến `i` để đưa nó lên đầu mảng. Tiếp theo, flip từ `0` đến vị trí đúng của nó ở cuối mảng chưa sắp xếp (size hiện tại). Lặp lại quá trình này cho đến khi mảng hoàn toàn được sắp xếp.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class PancakeSorting {
    public List<Integer> pancakeSort(int[] arr) {
        List<Integer> res = new ArrayList<>();
        int n = arr.length;
        
        for (int currSize = n; currSize > 1; currSize--) {
            int maxIdx = findMaxIdx(arr, currSize);
            if (maxIdx != currSize - 1) {
                if (maxIdx != 0) {
                    flip(arr, maxIdx);
                    res.add(maxIdx + 1);
                }
                flip(arr, currSize - 1);
                res.add(currSize);
            }
        }
        return res;
    }
    
    private int findMaxIdx(int[] arr, int size) {
        int maxIdx = 0;
        for (int i = 1; i < size; i++) {
            if (arr[i] > arr[maxIdx]) {
                maxIdx = i;
            }
        }
        return maxIdx;
    }
    
    private void flip(int[] arr, int k) {
        int i = 0, j = k;
        while (i < j) {
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
            i++;
            j--;
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N^2)$ vì ta cần lặp $N$ lần, mỗi lần tìm max và flip tốn $O(N)$.
- Space Complexity: $O(1)$ bỏ qua mảng kết quả.

---

## 20. Sort Characters By Frequency
**Đề bài chi tiết:**
Cho một chuỗi `s`, hãy sắp xếp nó theo thứ tự giảm dần dựa trên tần suất xuất hiện của các ký tự. Các ký tự xuất hiện nhiều lần phải ở gần nhau. Nếu có nhiều chuỗi kết quả đúng, trả về bất kỳ kết quả nào.

**Phân tích thuật toán:**
Đếm tần suất xuất hiện của từng ký tự bằng HashMap. Dùng Max Heap (PriorityQueue) để sắp xếp các ký tự dựa theo tần suất đếm được. Lấy từng ký tự ra khỏi Heap và lặp lại số lần tương ứng để xây dựng chuỗi kết quả.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;
import java.util.PriorityQueue;

public class SortCharactersByFrequency {
    public String frequencySort(String s) {
        Map<Character, Integer> freqMap = new HashMap<>();
        for (char c : s.toCharArray()) {
            freqMap.put(c, freqMap.getOrDefault(c, 0) + 1);
        }
        
        PriorityQueue<Character> maxHeap = new PriorityQueue<>((a, b) -> 
            freqMap.get(b) - freqMap.get(a)
        );
        maxHeap.addAll(freqMap.keySet());
        
        StringBuilder sb = new StringBuilder();
        while (!maxHeap.isEmpty()) {
            char current = maxHeap.poll();
            int count = freqMap.get(current);
            for (int i = 0; i < count; i++) {
                sb.append(current);
            }
        }
        return sb.toString();
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \log K)$ với $K$ là số lượng ký tự khác biệt.
- Space Complexity: $O(N)$ cho chuỗi kết quả và bộ nhớ heap / hash map.

---

## 21. K Closest Points to Origin
**Đề bài chi tiết:**
Cho mảng các điểm `points` trong đó `points[i] = [x_i, y_i]` biểu diễn một điểm trên mặt phẳng 2D, và một số nguyên `k`. Trả về `k` điểm gần nhất với gốc tọa độ (0, 0). Khoảng cách giữa hai điểm được đo bằng khoảng cách Euclidean: $\sqrt{(x_1 - x_2)^2 + (y_1 - y_2)^2}$.

**Phân tích thuật toán:**
Ta không cần tính căn bậc hai để so sánh khoảng cách, chỉ cần so sánh $X^2 + Y^2$. Có thể dùng Max Heap kích thước `k`. Các điểm mới chèn vào, nếu Heap vượt quá `k`, phần tử xa nhất (nằm ở đầu Max Heap) sẽ bị loại bỏ.

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;

public class KClosestPoints {
    public int[][] kClosest(int[][] points, int k) {
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) -> 
            (b[0] * b[0] + b[1] * b[1]) - (a[0] * a[0] + a[1] * a[1])
        );
        
        for (int[] point : points) {
            maxHeap.offer(point);
            if (maxHeap.size() > k) {
                maxHeap.poll();
            }
        }
        
        int[][] res = new int[k][2];
        int index = 0;
        while (!maxHeap.isEmpty()) {
            res[index++] = maxHeap.poll();
        }
        return res;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \log K)$.
- Space Complexity: $O(K)$.

---

## 22. Top K Frequent Elements
**Đề bài chi tiết:**
Cho mảng số nguyên `nums` và một số nguyên `k`, trả về `k` phần tử xuất hiện thường xuyên nhất. Bạn có thể trả về câu trả lời theo bất kỳ thứ tự nào.

**Phân tích thuật toán:**
Tương tự như sắp xếp theo tần suất, đầu tiên ta dùng HashMap đếm tần suất các số. Sau đó, dùng Min Heap kích thước `k` để giữ lại top `k` phần tử lớn nhất. Khi size của Min Heap lớn hơn `k`, ta poll đi phần tử có tần suất nhỏ nhất. Hoặc dùng Bucket Sort mảng mảng tần suất từ $1$ đến $N$ để đạt độ phức tạp $O(N)$. Dưới đây trình bày cách dùng Min Heap.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;
import java.util.PriorityQueue;

public class TopKFrequentElements {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freq = new HashMap<>();
        for (int num : nums) {
            freq.put(num, freq.getOrDefault(num, 0) + 1);
        }
        
        PriorityQueue<Integer> minHeap = new PriorityQueue<>((a, b) -> freq.get(a) - freq.get(b));
        for (int num : freq.keySet()) {
            minHeap.offer(num);
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }
        
        int[] res = new int[k];
        for (int i = 0; i < k; i++) {
            res[i] = minHeap.poll();
        }
        return res;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \log K)$.
- Space Complexity: $O(N)$ cho map và heap.

---

## 23. Top K Frequent Words
**Đề bài chi tiết:**
Cho mảng các chuỗi `words` và số nguyên `k`. Trả về `k` chuỗi xuất hiện thường xuyên nhất. Trả về kết quả được sắp xếp giảm dần theo tần suất. Nếu có cùng tần suất, sắp xếp theo thứ tự từ điển tăng dần.

**Phân tích thuật toán:**
Đếm tần suất với HashMap. Dùng Min Heap kích thước `k`. Chú ý hàm Comparator: nếu `freq(a) == freq(b)` ta so sánh chuỗi ngược `b.compareTo(a)` trong Min Heap (bởi vì phần tử cần bị loại ra - phần tử nhỏ hơn trong Min Heap - phải là phần tử đứng sai theo yêu cầu, tức là thứ tự từ điển lớn hơn sẽ bị đẩy ra trước để nhường chỗ cho cái đúng thứ tự từ điển). Cuối cùng lấy ra phải đảo ngược danh sách.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.PriorityQueue;

public class TopKFrequentWords {
    public List<String> topKFrequent(String[] words, int k) {
        Map<String, Integer> freq = new HashMap<>();
        for (String word : words) {
            freq.put(word, freq.getOrDefault(word, 0) + 1);
        }
        
        PriorityQueue<String> minHeap = new PriorityQueue<>((a, b) -> {
            if (freq.get(a).equals(freq.get(b))) {
                return b.compareTo(a); // Từ điển lớn hơn bị loại trước
            }
            return freq.get(a) - freq.get(b); // Tần suất nhỏ hơn bị loại trước
        });
        
        for (String word : freq.keySet()) {
            minHeap.offer(word);
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }
        
        List<String> res = new ArrayList<>();
        while (!minHeap.isEmpty()) {
            res.add(minHeap.poll());
        }
        Collections.reverse(res);
        return res;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \log K)$ với thao tác so sánh chuỗi.
- Space Complexity: $O(N)$.

---

## 24. Relative Sort Array
**Đề bài chi tiết:**
Cho hai mảng `arr1` và `arr2`, trong đó các phần tử của `arr2` là khác biệt nhau, và tất cả các phần tử trong `arr2` cũng có mặt trong `arr1`. Hãy sắp xếp `arr1` sao cho thứ tự tương đối giữa các phần tử bằng với thứ tự trong `arr2`. Những phần tử không nằm trong `arr2` thì sắp xếp tăng dần và đặt ở cuối.

**Phân tích thuật toán:**
Dùng Counting Sort. Khởi tạo mảng đếm tần suất cho `arr1`. Sau đó duyệt theo thứ tự phần tử của `arr2`, xuất các phần tử vào mảng kết quả theo số lần đếm được. Cuối cùng, duyệt nốt mảng đếm (vốn đã tăng dần theo index) để nối các phần tử còn lại không có trong `arr2`.

**Mã nguồn Java:**
```java
public class RelativeSortArray {
    public int[] relativeSortArray(int[] arr1, int[] arr2) {
        int[] count = new int[1001]; // Giới hạn đề bài max là 1000
        for (int num : arr1) {
            count[num]++;
        }
        
        int[] res = new int[arr1.length];
        int index = 0;
        
        for (int num : arr2) {
            while (count[num] > 0) {
                res[index++] = num;
                count[num]--;
            }
        }
        
        for (int i = 0; i <= 1000; i++) {
            while (count[i] > 0) {
                res[index++] = i;
                count[i]--;
            }
        }
        
        return res;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N + M + U)$ với $U$ là dải giá trị cực đại.
- Space Complexity: $O(U)$.

---

## 25. Sort Items by Groups Respecting Dependencies
**Đề bài chi tiết:**
Có `n` items đánh số từ `0` đến `n-1` được gán cho các nhóm `m` (hoặc -1 nếu không thuộc nhóm nào). Có mảng quan hệ phụ thuộc trước/sau giữa các items. Sắp xếp các items sao cho: các items cùng nhóm nằm cạnh nhau, và thỏa mãn đúng quan hệ phụ thuộc tổng thể và trong nội bộ nhóm.

**Phân tích thuật toán:**
Gán nhóm ảo (nhóm mới) cho những item có `group[i] == -1` để chuẩn hóa (mỗi item đều thuộc một nhóm riêng rẽ). Tạo ra 2 đồ thị: một đồ thị cho Group và một cho Item. Tính toán trong bậc (In-degree) cho cả hai đồ thị. Dùng Topological Sort kiểm tra chu trình và sắp xếp cả Group lẫn Item. Cuối cùng phân rã danh sách Item vào các Group đã được Topological Sort.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.LinkedList;
import java.util.List;
import java.util.Map;
import java.util.Queue;

public class SortItemsByGroups {
    public int[] sortItems(int n, int m, int[] group, List<List<Integer>> beforeItems) {
        for (int i = 0; i < n; i++) {
            if (group[i] == -1) group[i] = m++;
        }
        
        List<List<Integer>> itemGraph = new ArrayList<>();
        List<List<Integer>> groupGraph = new ArrayList<>();
        for (int i = 0; i < n; i++) itemGraph.add(new ArrayList<>());
        for (int i = 0; i < m; i++) groupGraph.add(new ArrayList<>());
        
        int[] itemInDegree = new int[n];
        int[] groupInDegree = new int[m];
        
        for (int i = 0; i < n; i++) {
            for (int prev : beforeItems.get(i)) {
                itemGraph.get(prev).add(i);
                itemInDegree[i]++;
                if (group[i] != group[prev]) {
                    groupGraph.get(group[prev]).add(group[i]);
                    groupInDegree[group[i]]++;
                }
            }
        }
        
        List<Integer> itemOrder = topoSort(itemGraph, itemInDegree, n);
        List<Integer> groupOrder = topoSort(groupGraph, groupInDegree, m);
        
        if (itemOrder.isEmpty() || groupOrder.isEmpty()) return new int[0];
        
        Map<Integer, List<Integer>> groupedItems = new HashMap<>();
        for (int item : itemOrder) {
            groupedItems.computeIfAbsent(group[item], k -> new ArrayList<>()).add(item);
        }
        
        int[] res = new int[n];
        int index = 0;
        for (int g : groupOrder) {
            if (groupedItems.containsKey(g)) {
                for (int item : groupedItems.get(g)) {
                    res[index++] = item;
                }
            }
        }
        return res;
    }
    
    private List<Integer> topoSort(List<List<Integer>> graph, int[] inDegree, int size) {
        List<Integer> res = new ArrayList<>();
        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < size; i++) {
            if (inDegree[i] == 0) q.offer(i);
        }
        while (!q.isEmpty()) {
            int curr = q.poll();
            res.add(curr);
            for (int neighbor : graph.get(curr)) {
                inDegree[neighbor]--;
                if (inDegree[neighbor] == 0) q.offer(neighbor);
            }
        }
        return res.size() == size ? res : new ArrayList<>();
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N + E)$ với $E$ là số phụ thuộc.
- Space Complexity: $O(N + E + M)$ cho đồ thị.

---

## 26. Car Fleet
**Đề bài chi tiết:**
Có `n` chiếc xe di chuyển trên một con đường 1 chiều tới đích `target`. Bạn được cho mảng `position` và `speed` tương ứng. Những chiếc xe không thể vượt nhau. Nếu một chiếc xe chạy nhanh đuổi kịp một chiếc chạy chậm, nó sẽ gộp thành một đoàn xe (fleet) và chạy với tốc độ của chiếc chậm. Tính số lượng đoàn xe khi đến đích.

**Phân tích thuật toán:**
Tạo cặp mảng gồm `position` và thời gian tới đích: `time = (target - position) / speed`. Sắp xếp các xe giảm dần theo vị trí xuất phát. Lặp từ chiếc xe gần đích nhất: nếu thời gian tới đích của chiếc phía sau $\le$ chiếc phía trước, chúng sẽ hợp thành fleet (cùng thời gian tới, do không vượt được). Ngược lại, nếu lâu hơn nó sẽ tính là một fleet mới tụt lại đằng sau.

**Mã nguồn Java:**
```java
import java.util.Arrays;

public class CarFleet {
    public int carFleet(int target, int[] position, int[] speed) {
        int n = position.length;
        double[][] cars = new double[n][2];
        for (int i = 0; i < n; i++) {
            cars[i][0] = position[i];
            cars[i][1] = (double)(target - position[i]) / speed[i];
        }
        
        Arrays.sort(cars, (a, b) -> Double.compare(b[0], a[0])); // Sắp xếp vị trí giảm dần
        
        int fleets = 0;
        double maxTime = 0;
        for (int i = 0; i < n; i++) {
            if (cars[i][1] > maxTime) {
                maxTime = cars[i][1];
                fleets++;
            }
        }
        return fleets;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \log N)$ cho thao tác sort.
- Space Complexity: $O(N)$ lưu trữ thông tin xe.

---

## 27. Sort an Array
**Đề bài chi tiết:**
Cho mảng số nguyên `nums`, hãy sắp xếp mảng theo thứ tự tăng dần bằng thuật toán có độ phức tạp thời gian $O(N \log N)$ và độ phức tạp không gian nhỏ nhất có thể (thường cấm dùng API `Arrays.sort()`).

**Phân tích thuật toán:**
Cài đặt Merge Sort (chậm hơn một chút nhưng Time đảm bảo worst case) hoặc Heap Sort (in-place space $O(1)$). Quick Sort có worst case $O(N^2)$ nếu test case hiểm ác. Ở đây ta trình bày Merge Sort.

**Mã nguồn Java:**
```java
public class SortArray {
    public int[] sortArray(int[] nums) {
        mergeSort(nums, 0, nums.length - 1);
        return nums;
    }
    
    private void mergeSort(int[] nums, int left, int right) {
        if (left >= right) return;
        int mid = left + (right - left) / 2;
        mergeSort(nums, left, mid);
        mergeSort(nums, mid + 1, right);
        merge(nums, left, mid, right);
    }
    
    private void merge(int[] nums, int left, int mid, int right) {
        int[] temp = new int[right - left + 1];
        int i = left, j = mid + 1, k = 0;
        
        while (i <= mid && j <= right) {
            if (nums[i] <= nums[j]) {
                temp[k++] = nums[i++];
            } else {
                temp[k++] = nums[j++];
            }
        }
        while (i <= mid) temp[k++] = nums[i++];
        while (j <= right) temp[k++] = nums[j++];
        
        for (int p = 0; p < temp.length; p++) {
            nums[left + p] = temp[p];
        }
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \log N)$.
- Space Complexity: $O(N)$ (Merge Sort cấp phát mảng temp).

---

## 28. Contains Duplicate III
**Đề bài chi tiết:**
Cho mảng số nguyên `nums` và hai số nguyên `k`, `t`. Tìm xem có tồn tại $i \ne j$ sao cho $|i - j| \le k$ và $|nums[i] - nums[j]| \le t$ hay không.

**Phân tích thuật toán:**
Đây là bài toán Sliding Window kết hợp với cây nhị phân tìm kiếm (TreeSet). Duyệt qua mảng, tại mỗi vị trí `i`, ta dùng hàm `ceiling()` của TreeSet để tìm phần tử lớn hơn hoặc bằng `nums[i] - t`. Nếu tồn tại và phần tử đó $\le nums[i] + t$, trả về true. Quản lý cửa sổ trượt bằng cách xóa `nums[i - k]` khi $i \ge k$.

**Mã nguồn Java:**
```java
import java.util.TreeSet;

public class ContainsDuplicateIII {
    public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
        if (k <= 0 || t < 0) return false;
        
        TreeSet<Long> set = new TreeSet<>();
        for (int i = 0; i < nums.length; i++) {
            Long num = (long) nums[i];
            
            Long floor = set.floor(num + t);
            Long ceil = set.ceiling(num - t);
            
            if ((floor != null && floor >= num) || (ceil != null && ceil <= num)) {
                return true;
            }
            
            set.add(num);
            if (i >= k) {
                set.remove((long) nums[i - k]);
            }
        }
        return false;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \log K)$.
- Space Complexity: $O(K)$.

---

## 29. Queue Reconstruction by Height
**Đề bài chi tiết:**
Một hàng đợi những người chưa được sắp xếp. Bạn được cho mảng `people`, trong đó `people[i] = [h_i, k_i]` nghĩa là người thứ `i` có chiều cao `h_i` và có đúng `k_i` người đứng phía trước có chiều cao lớn hơn hoặc bằng `h_i`. Xây dựng lại hàng đợi theo đúng yêu cầu này.

**Phân tích thuật toán:**
Sắp xếp giảm dần theo chiều cao `h`. Nếu chiều cao bằng nhau, sắp xếp tăng dần theo `k`. Kỹ thuật này giúp ta khi xét tới bất kỳ người nào, tất cả những người trong list hiện tại đều đã cao hơn hoặc bằng người đó. Việc cần làm tiếp theo chỉ là chèn trực tiếp (insert) người đó vào danh sách mới với index chính là `k` của họ.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class QueueReconstruction {
    public int[][] reconstructQueue(int[][] people) {
        Arrays.sort(people, (a, b) -> {
            if (a[0] == b[0]) return a[1] - b[1]; // Chiều cao bằng thì k nhỏ lên trước
            return b[0] - a[0]; // Chiều cao giảm dần
        });
        
        List<int[]> queue = new ArrayList<>();
        for (int[] p : people) {
            queue.add(p[1], p); // Insert tại index k
        }
        
        return queue.toArray(new int[people.length][2]);
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N^2)$ vì hàm chèn `ArrayList.add(index, element)` tốn $O(N)$ cho $N$ phần tử. Sắp xếp tốn $O(N \log N)$.
- Space Complexity: $O(N)$.

---

## 30. Diagonal Traverse II
**Đề bài chi tiết:**
Cho một mảng 2D (có thể là mảng răng cưa) `nums`. Trả về tất cả các phần tử trong `nums` theo thứ tự duyệt theo đường chéo từ dưới lên, từ trái sang phải.

**Phân tích thuật toán:**
Đặc điểm quan trọng của đường chéo là các phần tử trên cùng một đường chéo (từ dưới lên phải) có tổng chỉ số `(i + j)` bằng nhau. Do đó, ta duyệt toàn bộ ma trận, lưu các phần tử vào HashMap theo key là `i + j` (hoặc mảng List). Để đi từ dưới lên thì khi đọc danh sách (lúc chèn thì đi từ hàng 0) ta sẽ đọc từ cuối lên, vì các phần tử hàng nhỏ hơn vào list trước. 

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class DiagonalTraverseII {
    public int[] findDiagonalOrder(List<List<Integer>> nums) {
        List<List<Integer>> diagonals = new ArrayList<>();
        int count = 0;
        
        for (int i = 0; i < nums.size(); i++) {
            List<Integer> row = nums.get(i);
            count += row.size();
            for (int j = 0; j < row.size(); j++) {
                int sum = i + j;
                if (sum >= diagonals.size()) {
                    diagonals.add(new ArrayList<>());
                }
                diagonals.get(sum).add(row.get(j));
            }
        }
        
        int[] res = new int[count];
        int index = 0;
        for (List<Integer> diag : diagonals) {
            for (int i = diag.size() - 1; i >= 0; i--) { // Đọc ngược list đường chéo
                res[index++] = diag.get(i);
            }
        }
        return res;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$ với $N$ là tổng số phần tử.
- Space Complexity: $O(N)$ cho cấu trúc dữ liệu trung gian.

