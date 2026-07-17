# Companion Problems: 01-Array-Basics-And-Memory

Tài liệu này cung cấp **30 bài tập thực hành** chuyên sâu về Array — từ thao tác cơ bản đến tối ưu hóa bộ nhớ và Cache Locality.

---

## 1. Reverse Array In-Place
**Đề bài chi tiết:** Cho mảng `int[]`, đảo ngược tất cả phần tử tại chỗ (In-place) mà không dùng mảng phụ.
**Phân tích thuật toán:** Dùng Two Pointers: `left = 0`, `right = n-1`. Swap `arr[left]` và `arr[right]`, rồi thu hẹp `left++`, `right--` cho đến khi gặp nhau. In-place vì chỉ dùng 1 biến `temp` phụ. Không cần mảng mới.
**Mã nguồn Java:**
```java
public class ReverseArray {
    public static void reverse(int[] arr) {
        int left = 0, right = arr.length - 1;
        while (left < right) {
            int temp = arr[left];
            arr[left] = arr[right];
            arr[right] = temp;
            left++;
            right--;
        }
    }

    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5};
        reverse(arr);
        System.out.println(java.util.Arrays.toString(arr)); // [5, 4, 3, 2, 1]
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 2. Rotate Array by K Positions
**Đề bài chi tiết:** Cho mảng `int[]` và số nguyên $K$. Xoay mảng sang phải $K$ vị trí. Ví dụ: `[1,2,3,4,5]` xoay 2 → `[4,5,1,2,3]`.
**Phân tích thuật toán:** Cách tối ưu: 3 lần Reverse. (1) Reverse toàn mảng. (2) Reverse $K$ phần tử đầu. (3) Reverse $N-K$ phần tử còn lại. Tổng: $\mathcal{O}(N)$ Time, $\mathcal{O}(1)$ Space. Cách khác dùng mảng phụ tốn $\mathcal{O}(N)$ Space.
**Mã nguồn Java:**
```java
public class RotateArray {
    public static void rotate(int[] arr, int k) {
        int n = arr.length;
        k = k % n; // Xử lý k > n
        if (k == 0) return;
        reverse(arr, 0, n - 1);     // Reverse toàn bộ
        reverse(arr, 0, k - 1);     // Reverse K đầu
        reverse(arr, k, n - 1);     // Reverse N-K cuối
    }

    private static void reverse(int[] arr, int lo, int hi) {
        while (lo < hi) {
            int t = arr[lo]; arr[lo] = arr[hi]; arr[hi] = t;
            lo++; hi--;
        }
    }

    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5};
        rotate(arr, 2);
        System.out.println(java.util.Arrays.toString(arr)); // [4, 5, 1, 2, 3]
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 3. Find Maximum and Minimum
**Đề bài chi tiết:** Tìm giá trị lớn nhất và nhỏ nhất trong mảng chỉ với $\lceil 3N/2 \rceil - 2$ phép so sánh (Thay vì $2N - 2$ phép).
**Phân tích thuật toán:** So sánh từng CẶP phần tử với nhau trước ($N/2$ phép). Phần tử lớn hơn so với max hiện tại ($N/2$ phép). Phần tử nhỏ hơn so với min hiện tại ($N/2$ phép). Tổng: $3N/2$ phép. Tiết kiệm 25% so với cách naïve.
**Mã nguồn Java:**
```java
public class MinMaxOptimal {
    public static int[] findMinMax(int[] arr) {
        int n = arr.length;
        int min, max;
        int start;
        if (n % 2 == 0) {
            min = Math.min(arr[0], arr[1]);
            max = Math.max(arr[0], arr[1]);
            start = 2;
        } else {
            min = max = arr[0];
            start = 1;
        }
        for (int i = start; i < n - 1; i += 2) {
            if (arr[i] < arr[i + 1]) {
                min = Math.min(min, arr[i]);
                max = Math.max(max, arr[i + 1]);
            } else {
                min = Math.min(min, arr[i + 1]);
                max = Math.max(max, arr[i]);
            }
        }
        return new int[]{min, max};
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$ với $\lceil 3N/2 \rceil - 2$ comparisons.

---

## 4. Remove Duplicates from Sorted Array
**Đề bài chi tiết:** Cho mảng sorted `int[]`, xóa các phần tử trùng lặp in-place. Trả về số lượng phần tử unique.
**Phân tích thuật toán:** Two Pointers: `slow` đánh dấu vị trí ghi phần tử unique tiếp theo. `fast` duyệt qua tất cả phần tử. Nếu `arr[fast] != arr[slow]`, tăng `slow` và ghi `arr[fast]` vào `arr[slow+1]`.
**Mã nguồn Java:**
```java
public class RemoveDuplicatesSorted {
    public static int removeDuplicates(int[] arr) {
        if (arr.length == 0) return 0;
        int slow = 0;
        for (int fast = 1; fast < arr.length; fast++) {
            if (arr[fast] != arr[slow]) {
                slow++;
                arr[slow] = arr[fast];
            }
        }
        return slow + 1;
    }

    public static void main(String[] args) {
        int[] arr = {1, 1, 2, 2, 3, 4, 4, 5};
        int len = removeDuplicates(arr);
        System.out.println(java.util.Arrays.toString(java.util.Arrays.copyOf(arr, len)));
        // [1, 2, 3, 4, 5]
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 5. Move Zeros to End
**Đề bài chi tiết:** Cho mảng `int[]`, di chuyển tất cả số 0 về cuối mảng mà giữ nguyên thứ tự các phần tử khác 0.
**Phân tích thuật toán:** Two Pointers: `insertPos` đánh dấu vị trí tiếp theo để ghi phần tử != 0. Duyệt mảng: Nếu `arr[i] != 0`, ghi vào `arr[insertPos++]`. Sau khi duyệt xong, fill 0 từ `insertPos` đến cuối.
**Mã nguồn Java:**
```java
public class MoveZeros {
    public static void moveZeroes(int[] arr) {
        int insertPos = 0;
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] != 0) {
                arr[insertPos++] = arr[i];
            }
        }
        while (insertPos < arr.length) {
            arr[insertPos++] = 0;
        }
    }

    public static void main(String[] args) {
        int[] arr = {0, 1, 0, 3, 12};
        moveZeroes(arr);
        System.out.println(java.util.Arrays.toString(arr)); // [1, 3, 12, 0, 0]
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 6. Kadane's Algorithm — Maximum Subarray Sum
**Đề bài chi tiết:** Tìm mảng con liên tiếp có tổng lớn nhất. Trả về tổng lớn nhất.
**Phân tích thuật toán:** Tại mỗi vị trí $i$, quyết định: Mở rộng subarray hiện tại (`currentSum + arr[i]`) hay bắt đầu subarray mới (`arr[i]`). Chọn cái lớn hơn. Luôn cập nhật `maxSum`. Đây là Dynamic Programming 1D tối ưu.
**Mã nguồn Java:**
```java
public class KadanesAlgorithm {
    public static int maxSubarraySum(int[] arr) {
        int currentSum = arr[0];
        int maxSum = arr[0];
        for (int i = 1; i < arr.length; i++) {
            currentSum = Math.max(arr[i], currentSum + arr[i]);
            maxSum = Math.max(maxSum, currentSum);
        }
        return maxSum;
    }

    public static void main(String[] args) {
        int[] arr = {-2, 1, -3, 4, -1, 2, 1, -5, 4};
        System.out.println(maxSubarraySum(arr)); // 6 (subarray [4,-1,2,1])
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 7. Dutch National Flag — Sort 0s, 1s, 2s
**Đề bài chi tiết:** Cho mảng chỉ chứa 0, 1, 2. Sắp xếp in-place trong 1 lần duyệt.
**Phân tích thuật toán:** 3 Pointers: `low` = vùng 0 kết thúc, `mid` = vị trí đang xét, `high` = vùng 2 bắt đầu. Nếu `arr[mid] == 0`: swap với `arr[low]`, tăng cả 2. Nếu `arr[mid] == 1`: tăng `mid`. Nếu `arr[mid] == 2`: swap với `arr[high]`, giảm `high`.
**Mã nguồn Java:**
```java
public class DutchNationalFlag {
    public static void sort012(int[] arr) {
        int low = 0, mid = 0, high = arr.length - 1;
        while (mid <= high) {
            if (arr[mid] == 0) {
                swap(arr, low++, mid++);
            } else if (arr[mid] == 1) {
                mid++;
            } else {
                swap(arr, mid, high--);
            }
        }
    }

    private static void swap(int[] arr, int i, int j) {
        int t = arr[i]; arr[i] = arr[j]; arr[j] = t;
    }

    public static void main(String[] args) {
        int[] arr = {2, 0, 1, 2, 0, 1, 0};
        sort012(arr);
        System.out.println(java.util.Arrays.toString(arr)); // [0, 0, 0, 1, 1, 2, 2]
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$, 1 lần duyệt.

---

## 8. Merge Two Sorted Arrays
**Đề bài chi tiết:** Gộp 2 mảng sorted thành 1 mảng sorted mới.
**Phân tích thuật toán:** Two Pointers: `i` duyệt mảng A, `j` duyệt mảng B. So sánh `A[i]` và `B[j]`, ghi phần tử nhỏ hơn vào mảng kết quả. Tiếp tục cho đến khi cả 2 hết. Đây chính là bước Merge của Merge Sort.
**Mã nguồn Java:**
```java
public class MergeSortedArrays {
    public static int[] merge(int[] a, int[] b) {
        int[] result = new int[a.length + b.length];
        int i = 0, j = 0, k = 0;
        while (i < a.length && j < b.length) {
            result[k++] = (a[i] <= b[j]) ? a[i++] : b[j++];
        }
        while (i < a.length) result[k++] = a[i++];
        while (j < b.length) result[k++] = b[j++];
        return result;
    }

    public static void main(String[] args) {
        int[] a = {1, 3, 5, 7};
        int[] b = {2, 4, 6, 8};
        System.out.println(java.util.Arrays.toString(merge(a, b)));
        // [1, 2, 3, 4, 5, 6, 7, 8]
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N + M)$, Space $\mathcal{O}(N + M)$.

---

## 9. Find Missing Number
**Đề bài chi tiết:** Mảng chứa $N-1$ số từ $1$ đến $N$ (mỗi số xuất hiện đúng 1 lần). Tìm số bị thiếu.
**Phân tích thuật toán:** Tổng lý thuyết $1 + 2 + \dots + N = N(N+1)/2$. Tổng thực tế = Sum mảng. Số thiếu = Tổng lý thuyết - Tổng thực tế. Cách khác dùng XOR: XOR tất cả số từ $1$ đến $N$ rồi XOR với tất cả phần tử mảng. Kết quả là số thiếu (Vì $x \oplus x = 0$).
**Mã nguồn Java:**
```java
public class MissingNumber {
    // Cách 1: Sum
    public static int missingSum(int[] arr, int n) {
        long expected = (long) n * (n + 1) / 2;
        long actual = 0;
        for (int x : arr) actual += x;
        return (int) (expected - actual);
    }

    // Cách 2: XOR (Tránh overflow)
    public static int missingXOR(int[] arr, int n) {
        int xor = 0;
        for (int i = 1; i <= n; i++) xor ^= i;
        for (int x : arr) xor ^= x;
        return xor;
    }

    public static void main(String[] args) {
        int[] arr = {1, 2, 4, 5, 6};
        System.out.println(missingSum(arr, 6));  // 3
        System.out.println(missingXOR(arr, 6));  // 3
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 10. Two Sum — Sorted Array
**Đề bài chi tiết:** Cho mảng sorted và target sum. Tìm 2 phần tử có tổng bằng target.
**Phân tích thuật toán:** Two Pointers: `left = 0`, `right = n-1`. Nếu `arr[left] + arr[right] == target` → tìm thấy. Nếu tổng < target → `left++`. Nếu tổng > target → `right--`. Mỗi bước loại bỏ 1 ứng viên.
**Mã nguồn Java:**
```java
public class TwoSumSorted {
    public static int[] twoSum(int[] arr, int target) {
        int left = 0, right = arr.length - 1;
        while (left < right) {
            int sum = arr[left] + arr[right];
            if (sum == target) return new int[]{left, right};
            if (sum < target) left++;
            else right--;
        }
        return new int[]{-1, -1};
    }

    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 6};
        int[] result = twoSum(arr, 6); // Indices [1, 3] (2 + 4 = 6)
        System.out.println(java.util.Arrays.toString(result));
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 11. Product of Array Except Self
**Đề bài chi tiết:** Cho mảng `int[] arr`, trả về mảng `result` sao cho `result[i]` bằng tích của tất cả các phần tử trong `arr` ngoại trừ `arr[i]`. Bạn phải thực hiện thuật toán có độ phức tạp thời gian $\mathcal{O}(N)$ và không dùng phép chia.
**Phân tích thuật toán:** Dùng phương pháp Prefix Product và Suffix Product. Tạo mảng kết quả lưu tích các phần tử bên trái. Sau đó duyệt từ phải qua trái, nhân dồn với tích các phần tử bên phải. Cách này không cần thêm mảng phụ ngoài mảng kết quả.
**Mã nguồn Java:**
```java
public class ProductExceptSelf {
    public static int[] productExceptSelf(int[] arr) {
        int n = arr.length;
        int[] result = new int[n];
        result[0] = 1;
        for (int i = 1; i < n; i++) {
            result[i] = result[i - 1] * arr[i - 1];
        }
        int rightProduct = 1;
        for (int i = n - 1; i >= 0; i--) {
            result[i] *= rightProduct;
            rightProduct *= arr[i];
        }
        return result;
    }

    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4};
        System.out.println(java.util.Arrays.toString(productExceptSelf(arr))); // [24, 12, 8, 6]
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$ (không tính mảng kết quả trả về).

---

## 12. Next Greater Element
**Đề bài chi tiết:** Cho mảng `int[] arr`, với mỗi phần tử `arr[i]`, tìm phần tử đầu tiên bên phải lớn hơn `arr[i]`. Nếu không có, gán giá trị `-1`.
**Phân tích thuật toán:** Dùng Monotonic Stack. Duyệt mảng từ phải sang trái. Duy trì một Stack chứa các phần tử sao cho đỉnh stack là phần tử nhỏ nhất. Nếu đỉnh stack nhỏ hơn hoặc bằng `arr[i]`, pop đỉnh stack ra cho đến khi tìm được phần tử lớn hơn `arr[i]`. Phần tử lớn hơn đó chính là Next Greater Element. Push `arr[i]` vào stack.
**Mã nguồn Java:**
```java
import java.util.Stack;

public class NextGreaterElement {
    public static int[] findNextGreater(int[] arr) {
        int n = arr.length;
        int[] result = new int[n];
        Stack<Integer> stack = new Stack<>();
        
        for (int i = n - 1; i >= 0; i--) {
            while (!stack.isEmpty() && stack.peek() <= arr[i]) {
                stack.pop();
            }
            result[i] = stack.isEmpty() ? -1 : stack.peek();
            stack.push(arr[i]);
        }
        return result;
    }

    public static void main(String[] args) {
        int[] arr = {4, 5, 2, 25};
        System.out.println(java.util.Arrays.toString(findNextGreater(arr))); // [5, 25, 25, -1]
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 13. Trapping Rain Water
**Đề bài chi tiết:** Cho mảng `int[] height` đại diện cho bản đồ độ cao nơi chiều rộng của mỗi cột là 1, hãy tính toán lượng nước có thể đọng lại sau khi trời mưa.
**Phân tích thuật toán:** Dùng Two Pointers (`left` và `right`). Maintain `leftMax` và `rightMax`. Ở mỗi bước, so sánh `height[left]` và `height[right]`. Nếu `height[left] < height[right]`, ta chắc chắn giới hạn nước tại `left` phụ thuộc vào `leftMax`, cập nhật `leftMax`, cộng lượng nước `leftMax - height[left]` vào kết quả và tăng `left++`. Ngược lại làm tương tự với `right`.
**Mã nguồn Java:**
```java
public class TrappingRainWater {
    public static int trap(int[] height) {
        int left = 0, right = height.length - 1;
        int leftMax = 0, rightMax = 0;
        int water = 0;
        
        while (left < right) {
            if (height[left] < height[right]) {
                leftMax = Math.max(leftMax, height[left]);
                water += leftMax - height[left];
                left++;
            } else {
                rightMax = Math.max(rightMax, height[right]);
                water += rightMax - height[right];
                right--;
            }
        }
        return water;
    }

    public static void main(String[] args) {
        int[] height = {0, 1, 0, 2, 1, 0, 1, 3, 2, 1, 2, 1};
        System.out.println(trap(height)); // 6
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 14. Container With Most Water
**Đề bài chi tiết:** Cho mảng `int[] height` đại diện cho các đường thẳng đứng. Tìm 2 đường tạo với trục hoành thành một bình chứa nhiều nước nhất. Trả về diện tích lớn nhất.
**Phân tích thuật toán:** Dùng Two Pointers `left = 0` và `right = n - 1`. Diện tích nước tính bằng công thức: $\min(\text{height}[left], \text{height}[right]) \times (right - left)$. Tính diện tích tại vị trí hiện tại và cập nhật max. Để tìm diện tích lớn hơn, ta di chuyển con trỏ đang trỏ tới cột thấp hơn vào trong.
**Mã nguồn Java:**
```java
public class ContainerMostWater {
    public static int maxArea(int[] height) {
        int left = 0, right = height.length - 1;
        int max = 0;
        
        while (left < right) {
            int currentArea = Math.min(height[left], height[right]) * (right - left);
            max = Math.max(max, currentArea);
            
            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }
        return max;
    }

    public static void main(String[] args) {
        int[] height = {1, 8, 6, 2, 5, 4, 8, 3, 7};
        System.out.println(maxArea(height)); // 49
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 15. Majority Element (Boyer-Moore Voting)
**Đề bài chi tiết:** Cho mảng `int[] arr` kích thước $N$, tìm phần tử xuất hiện nhiều hơn $\lfloor N/2 \rfloor$ lần. Giả sử mảng luôn có Majority Element.
**Phân tích thuật toán:** Thuật toán Boyer-Moore Voting: Duy trì một `candidate` và biến `count`. Khởi tạo `count = 0`. Duyệt mảng: Nếu `count == 0`, cập nhật `candidate = arr[i]`. Nếu `arr[i] == candidate`, tăng `count++`, ngược lại giảm `count--`. Thuật toán đúng vì số lần xuất hiện của Majority Element lớn hơn tổng số lần xuất hiện của tất cả phần tử khác.
**Mã nguồn Java:**
```java
public class MajorityElement {
    public static int majorityElement(int[] arr) {
        int candidate = -1, count = 0;
        for (int num : arr) {
            if (count == 0) {
                candidate = num;
            }
            count += (num == candidate) ? 1 : -1;
        }
        return candidate;
    }

    public static void main(String[] args) {
        int[] arr = {2, 2, 1, 1, 1, 2, 2};
        System.out.println(majorityElement(arr)); // 2
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 16. Find Duplicate Number (Floyd's Cycle)
**Đề bài chi tiết:** Cho mảng kích thước $N+1$ chứa các số từ $1$ đến $N$. Biết rằng có đúng 1 số bị lặp lại, hãy tìm số đó mà không thay đổi mảng (read-only) và chỉ dùng bộ nhớ $\mathcal{O}(1)$.
**Phân tích thuật toán:** Xem mảng như một danh sách liên kết: mỗi giá trị `arr[i]` trỏ đến index `arr[i]`. Vì có số lặp, nên sẽ tạo thành chu trình. Áp dụng Floyd's Cycle Detection (Thỏ và Rùa). `slow` đi 1 bước (`arr[slow]`), `fast` đi 2 bước (`arr[arr[fast]]`). Sau khi chúng gặp nhau, di chuyển `slow` về `0` và cho cả 2 đi từng bước một. Điểm gặp nhau tiếp theo chính là phần tử bắt đầu chu trình (số bị lặp).
**Mã nguồn Java:**
```java
public class FindDuplicate {
    public static int findDuplicate(int[] arr) {
        int slow = arr[0];
        int fast = arr[0];
        
        // Pha 1: Tìm điểm giao nhau trong chu trình
        do {
            slow = arr[slow];
            fast = arr[arr[fast]];
        } while (slow != fast);
        
        // Pha 2: Tìm phần tử bắt đầu chu trình (số lặp)
        slow = arr[0];
        while (slow != fast) {
            slow = arr[slow];
            fast = arr[fast];
        }
        return slow;
    }

    public static void main(String[] args) {
        int[] arr = {1, 3, 4, 2, 2};
        System.out.println(findDuplicate(arr)); // 2
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 17. Merge Intervals
**Đề bài chi tiết:** Cho mảng 2D chứa các khoảng thời gian (intervals), gộp tất cả các khoảng thời gian bị chồng chéo (overlap).
**Phân tích thuật toán:** Sắp xếp mảng theo giá trị bắt đầu (start) của mỗi khoảng. Duyệt qua mảng: maintain một khoảng đang xét. Nếu khoảng tiếp theo bắt đầu trước hoặc bằng thời gian kết thúc của khoảng đang xét (có overlap), cập nhật thời gian kết thúc (end) lớn nhất. Nếu không overlap, thêm khoảng đang xét vào kết quả và xét tiếp khoảng mới.
**Mã nguồn Java:**
```java
import java.util.Arrays;
import java.util.ArrayList;
import java.util.List;

public class MergeIntervals {
    public static int[][] merge(int[][] intervals) {
        if (intervals.length <= 1) return intervals;
        
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
        List<int[]> result = new ArrayList<>();
        int[] currentInterval = intervals[0];
        result.add(currentInterval);
        
        for (int[] interval : intervals) {
            int currentEnd = currentInterval[1];
            int nextBegin = interval[0];
            int nextEnd = interval[1];
            
            if (currentEnd >= nextBegin) { // Overlap
                currentInterval[1] = Math.max(currentEnd, nextEnd);
            } else {
                currentInterval = interval;
                result.add(currentInterval);
            }
        }
        return result.toArray(new int[result.size()][]);
    }

    public static void main(String[] args) {
        int[][] intervals = {{1, 3}, {2, 6}, {8, 10}, {15, 18}};
        int[][] merged = merge(intervals);
        for (int[] interval : merged) {
            System.out.print(Arrays.toString(interval) + " ");
        }
        // [1, 6] [8, 10] [15, 18]
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log N)$ (do thao tác sắp xếp), Space $\mathcal{O}(N)$ (lưu mảng kết quả).

---

## 18. Set Matrix Zeroes
**Đề bài chi tiết:** Cho một ma trận $M \times N$. Nếu một phần tử bằng $0$, hãy chuyển toàn bộ hàng và cột chứa phần tử đó thành $0$. Làm In-place.
**Phân tích thuật toán:** Dùng chính hàng đầu tiên và cột đầu tiên của ma trận để làm biến đánh dấu. Quét mảng, nếu `matrix[i][j] == 0`, set `matrix[i][0] = 0` và `matrix[0][j] = 0`. Cần 2 biến cờ (hoặc 1) để đánh dấu xem bản thân hàng 0 và cột 0 có chứa số 0 từ đầu không. Sau khi đánh dấu, gán các giá trị thành 0 dựa theo hàng/cột đánh dấu. Cuối cùng cập nhật lại hàng 0 và cột 0 nếu cần.
**Mã nguồn Java:**
```java
public class SetMatrixZeroes {
    public static void setZeroes(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        boolean firstRowZero = false, firstColZero = false;
        
        for (int i = 0; i < m; i++) {
            if (matrix[i][0] == 0) firstColZero = true;
        }
        for (int j = 0; j < n; j++) {
            if (matrix[0][j] == 0) firstRowZero = true;
        }
        
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][j] == 0) {
                    matrix[i][0] = 0;
                    matrix[0][j] = 0;
                }
            }
        }
        
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                    matrix[i][j] = 0;
                }
            }
        }
        
        if (firstColZero) {
            for (int i = 0; i < m; i++) matrix[i][0] = 0;
        }
        if (firstRowZero) {
            for (int j = 0; j < n; j++) matrix[0][j] = 0;
        }
    }

    public static void main(String[] args) {
        int[][] matrix = {{1, 1, 1}, {1, 0, 1}, {1, 1, 1}};
        setZeroes(matrix);
        for (int[] row : matrix) {
            System.out.println(java.util.Arrays.toString(row));
        }
        // [1, 0, 1]
        // [0, 0, 0]
        // [1, 0, 1]
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$, Space $\mathcal{O}(1)$.

---

## 19. Spiral Matrix Traversal
**Đề bài chi tiết:** Trả về tất cả các phần tử của ma trận $M \times N$ theo thứ tự xoắn ốc (spiral).
**Phân tích thuật toán:** Duy trì 4 ranh giới: `top`, `bottom`, `left`, `right`. Quét một vòng ranh giới ngoài: từ trái qua phải (top), trên xuống dưới (right), phải qua trái (bottom), dưới lên trên (left). Mỗi lần duyệt qua một biên, ta thu hẹp biên tương ứng. Kiểm tra điều kiện chồng chéo ranh giới trước khi duyệt chiều ngược lại (bottom, left).
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class SpiralMatrix {
    public static List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> res = new ArrayList<>();
        if (matrix.length == 0) return res;
        
        int top = 0, bottom = matrix.length - 1;
        int left = 0, right = matrix[0].length - 1;
        
        while (top <= bottom && left <= right) {
            for (int i = left; i <= right; i++) res.add(matrix[top][i]);
            top++;
            for (int i = top; i <= bottom; i++) res.add(matrix[i][right]);
            right--;
            
            if (top <= bottom) {
                for (int i = right; i >= left; i--) res.add(matrix[bottom][i]);
                bottom--;
            }
            if (left <= right) {
                for (int i = bottom; i >= top; i--) res.add(matrix[i][left]);
                left++;
            }
        }
        return res;
    }

    public static void main(String[] args) {
        int[][] matrix = {{1, 2, 3}, {4, 5, 6}, {7, 8, 9}};
        System.out.println(spiralOrder(matrix)); // [1, 2, 3, 6, 9, 8, 7, 4, 5]
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$, Space $\mathcal{O}(1)$ (không tính kết quả).

---

## 20. Pascal's Triangle
**Đề bài chi tiết:** Cho số nguyên `numRows`, tạo `numRows` hàng đầu tiên của tam giác Pascal.
**Phân tích thuật toán:** Trong tam giác Pascal, mỗi phần tử là tổng của hai phần tử ngay phía trên nó. Cụ thể: `C(i, j) = C(i-1, j-1) + C(i-1, j)`. Phần tử đầu và cuối của mỗi hàng luôn bằng `1`. Build hàng tiếp theo bằng cách dựa trên giá trị của hàng trước đó.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class PascalsTriangle {
    public static List<List<Integer>> generate(int numRows) {
        List<List<Integer>> triangle = new ArrayList<>();
        if (numRows == 0) return triangle;
        
        List<Integer> firstRow = new ArrayList<>();
        firstRow.add(1);
        triangle.add(firstRow);
        
        for (int i = 1; i < numRows; i++) {
            List<Integer> prevRow = triangle.get(i - 1);
            List<Integer> currentRow = new ArrayList<>();
            
            currentRow.add(1); // Phần tử đầu tiên
            for (int j = 1; j < i; j++) {
                currentRow.add(prevRow.get(j - 1) + prevRow.get(j));
            }
            currentRow.add(1); // Phần tử cuối cùng
            
            triangle.add(currentRow);
        }
        return triangle;
    }

    public static void main(String[] args) {
        List<List<Integer>> pt = generate(5);
        for (List<Integer> row : pt) {
            System.out.println(row);
        }
        // [1]
        // [1, 1]
        // [1, 2, 1]
        // [1, 3, 3, 1]
        // [1, 4, 6, 4, 1]
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N^2)$, Space $\mathcal{O}(N^2)$ (cho list kết quả, $N$ là số lượng hàng).

---

## 21. Best Time to Buy and Sell Stock (1 Transaction)
**Đề bài chi tiết:** Cho mảng `int[] prices` trong đó `prices[i]` là giá cổ phiếu vào ngày `i`. Bạn chỉ được thực hiện tối đa 1 giao dịch (1 lần mua và 1 lần bán). Tìm lợi nhuận lớn nhất có thể đạt được.
**Phân tích thuật toán:** Để tối đa hóa lợi nhuận tại ngày `i`, ta cần mua cổ phiếu ở giá thấp nhất trong các ngày từ `0` đến `i-1`. Do đó, ta duyệt mảng từ trái qua phải, duy trì một biến `minPrice` để lưu giá thấp nhất gặp cho đến hiện tại, và tính lợi nhuận nếu bán vào ngày hôm nay (`prices[i] - minPrice`). Cập nhật `maxProfit`.
**Mã nguồn Java:**
```java
public class BuySellStock {
    public static int maxProfit(int[] prices) {
        if (prices == null || prices.length == 0) return 0;
        
        int minPrice = Integer.MAX_VALUE;
        int maxProfit = 0;
        
        for (int price : prices) {
            if (price < minPrice) {
                minPrice = price;
            } else if (price - minPrice > maxProfit) {
                maxProfit = price - minPrice;
            }
        }
        
        return maxProfit;
    }

    public static void main(String[] args) {
        int[] prices = {7, 1, 5, 3, 6, 4};
        System.out.println(maxProfit(prices)); // 5 (Mua giá 1, bán giá 6)
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 22. Best Time to Buy and Sell Stock (Multiple Transactions)
**Đề bài chi tiết:** Cho mảng `int[] prices` là giá cổ phiếu qua từng ngày. Bạn có thể thực hiện bao nhiêu giao dịch tùy ý (mua và bán nhiều lần), nhưng không được giữ nhiều cổ phiếu cùng lúc. Tìm lợi nhuận lớn nhất.
**Phân tích thuật toán:** Áp dụng phương pháp Tham lam (Greedy). Bất cứ khi nào giá ngày hôm sau cao hơn giá ngày hôm nay (`prices[i] > prices[i-1]`), ta mô phỏng việc mua vào hôm nay và bán ra vào ngày mai. Lợi nhuận sẽ được cộng dồn bằng tổng tất cả các đoạn tăng giá này.
**Mã nguồn Java:**
```java
public class BuySellStockMultiple {
    public static int maxProfit(int[] prices) {
        int maxProfit = 0;
        
        for (int i = 1; i < prices.length; i++) {
            if (prices[i] > prices[i - 1]) {
                maxProfit += prices[i] - prices[i - 1];
            }
        }
        
        return maxProfit;
    }

    public static void main(String[] args) {
        int[] prices = {7, 1, 5, 3, 6, 4};
        System.out.println(maxProfit(prices)); // 7 (Mua 1 bán 5, Mua 3 bán 6)
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 23. Subarray Sum Equals K
**Đề bài chi tiết:** Cho mảng `int[] arr` (có thể chứa số âm) và số nguyên `K`. Đếm số lượng mảng con liên tiếp có tổng đúng bằng `K`.
**Phân tích thuật toán:** Sử dụng Hash Map và Prefix Sum. Giả sử tổng từ phần tử đầu tiên đến hiện tại là `currentSum`. Nếu trước đó đã xuất hiện một Prefix Sum bằng `currentSum - K`, điều đó có nghĩa là mảng con ở giữa có tổng đúng bằng `K`. Hash map sẽ lưu `<PrefixSum, Frequency>` để đếm xem tổng đó đã xuất hiện bao nhiêu lần.
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class SubarraySumK {
    public static int subarraySum(int[] arr, int k) {
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0, 1); // Khởi tạo tổng 0 xuất hiện 1 lần
        
        int count = 0;
        int currentSum = 0;
        
        for (int num : arr) {
            currentSum += num;
            
            if (map.containsKey(currentSum - k)) {
                count += map.get(currentSum - k);
            }
            
            map.put(currentSum, map.getOrDefault(currentSum, 0) + 1);
        }
        
        return count;
    }

    public static void main(String[] args) {
        int[] arr = {1, 1, 1};
        System.out.println(subarraySum(arr, 2)); // 2
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 24. Longest Consecutive Sequence
**Đề bài chi tiết:** Cho mảng `int[] arr` chưa được sắp xếp, tìm độ dài của chuỗi các phần tử liên tiếp dài nhất (ví dụ: 1, 2, 3, 4). Thuật toán phải có độ phức tạp thời gian $\mathcal{O}(N)$.
**Phân tích thuật toán:** Thêm tất cả các phần tử vào một HashSet. Với mỗi phần tử, chỉ bắt đầu đếm chiều dài chuỗi nếu nó là phần tử bắt đầu của một chuỗi (tức là `num - 1` không tồn tại trong Set). Nếu thỏa mãn, ta tiếp tục tìm kiếm `num + 1`, `num + 2`... trong Set và đếm chiều dài.
**Mã nguồn Java:**
```java
import java.util.HashSet;

public class LongestConsecutive {
    public static int longestConsecutive(int[] arr) {
        if (arr == null || arr.length == 0) return 0;
        
        HashSet<Integer> set = new HashSet<>();
        for (int num : arr) set.add(num);
        
        int longestStreak = 0;
        
        for (int num : set) {
            // Chỉ kiểm tra khi num là số bắt đầu của 1 chuỗi
            if (!set.contains(num - 1)) {
                int currentNum = num;
                int currentStreak = 1;
                
                while (set.contains(currentNum + 1)) {
                    currentNum += 1;
                    currentStreak += 1;
                }
                
                longestStreak = Math.max(longestStreak, currentStreak);
            }
        }
        
        return longestStreak;
    }

    public static void main(String[] args) {
        int[] arr = {100, 4, 200, 1, 3, 2};
        System.out.println(longestConsecutive(arr)); // 4 (Chuỗi 1, 2, 3, 4)
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 25. System.arraycopy Benchmark
**Đề bài chi tiết:** Viết chương trình benchmark để so sánh hiệu năng của việc sao chép mảng 10 triệu phần tử bằng 3 cách: vòng lặp `for` thủ công, `System.arraycopy`, và `Arrays.copyOf`.
**Phân tích thuật toán:** `System.arraycopy` sử dụng các hàm JNI (Java Native Interface) và block copy của bộ nhớ, tận dụng SIMD hoặc cơ chế DMA ở tầng phần cứng, do đó thường nhanh hơn so với vòng lặp `for` thủ công (do không phải check bound từng phần tử). `Arrays.copyOf` bản chất gọi `System.arraycopy` ngầm nhưng mất thêm thời gian khởi tạo mảng mới.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class ArrayCopyBenchmark {
    public static void main(String[] args) {
        int size = 10_000_000;
        int[] src = new int[size];
        for (int i = 0; i < size; i++) src[i] = i;

        int[] dest1 = new int[size];
        long t1 = System.currentTimeMillis();
        for (int i = 0; i < size; i++) dest1[i] = src[i];
        long t2 = System.currentTimeMillis();
        System.out.println("For-loop: " + (t2 - t1) + " ms");

        int[] dest2 = new int[size];
        long t3 = System.currentTimeMillis();
        System.arraycopy(src, 0, dest2, 0, size);
        long t4 = System.currentTimeMillis();
        System.out.println("System.arraycopy: " + (t4 - t3) + " ms");

        long t5 = System.currentTimeMillis();
        int[] dest3 = Arrays.copyOf(src, size);
        long t6 = System.currentTimeMillis();
        System.out.println("Arrays.copyOf: " + (t6 - t5) + " ms");
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 26. Cache Locality Benchmark
**Đề bài chi tiết:** Chứng minh ảnh hưởng của Cache Locality (tính cục bộ bộ nhớ) bằng cách benchmark thời gian duyệt ma trận `10000 x 10000` theo 2 cách: Row-Major (Duyệt hàng trước) và Column-Major (Duyệt cột trước).
**Phân tích thuật toán:** Trong Java, mảng 2D là mảng của các mảng 1D. Các phần tử trong cùng một mảng 1D (cùng một hàng) được cấp phát liên tiếp trên Heap. CPU nạp bộ nhớ theo từng Cache Line (thường 64 bytes). Khi duyệt theo hàng (Row-Major), các phần tử kế tiếp đã sẵn sàng trong Cache (Cache Hit). Khi duyệt theo cột, mỗi bước lặp ta truy cập một hàng khác nhau, gây ra Cache Miss liên tục, làm chậm chương trình rất nhiều.
**Mã nguồn Java:**
```java
public class CacheLocality {
    public static void main(String[] args) {
        int size = 10000;
        int[][] matrix = new int[size][size];

        long t1 = System.currentTimeMillis();
        long sum1 = 0;
        for (int r = 0; r < size; r++) {
            for (int c = 0; c < size; c++) {
                sum1 += matrix[r][c];
            }
        }
        long t2 = System.currentTimeMillis();
        System.out.println("Row-Major (Cache friendly): " + (t2 - t1) + " ms");

        long t3 = System.currentTimeMillis();
        long sum2 = 0;
        for (int c = 0; c < size; c++) {
            for (int r = 0; r < size; r++) {
                sum2 += matrix[r][c];
            }
        }
        long t4 = System.currentTimeMillis();
        System.out.println("Column-Major (Cache unfriendly): " + (t4 - t3) + " ms");
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N^2)$, Space $\mathcal{O}(N^2)$.

---

## 27. Array Memory Footprint
**Đề bài chi tiết:** Tính toán cấu trúc bộ nhớ (Memory footprint) của 3 cấu trúc dữ liệu lưu trữ 10 triệu phần tử số nguyên: `int[]`, `Integer[]` và `ArrayList<Integer>`.
**Phân tích thuật toán:** 
- `int[]`: Array Header (16 bytes) + $10M \times 4$ bytes (int primitive). Tổng: ~40 MB.
- `Integer[]`: Array Header (16 bytes) + $10M \times 4$ bytes (reference array). Cộng thêm bộ nhớ của $10M$ object `Integer` (Object Header 12 bytes + int 4 bytes = 16 bytes/object). Tổng: $\approx 40\text{MB} + 160\text{MB} = 200\text{MB}$.
- `ArrayList<Integer>`: Overhead của object ArrayList (24 bytes) + backing array `Object[]` (tham chiếu, $\approx 40\text{MB}$) + các đối tượng `Integer` ($\approx 160\text{MB}$). Tổng: $> 200\text{MB}$.
*Lưu ý: Không có code thực thi vì bài toán là phân tích lý thuyết, có thể dùng JOL (Java Object Layout) để đo thực tế.*
**Mã nguồn Java:**
```java
import java.util.ArrayList;

public class MemoryFootprintAnalysis {
    public static void main(String[] args) {
        int size = 10_000_000;
        
        // Primitive Array: ~ 40 MB bộ nhớ liên tục
        int[] primArray = new int[size];
        
        // Wrapper Array: ~ 200 MB (40 MB tham chiếu + 160 MB các object Integer phân mảnh)
        Integer[] objArray = new Integer[size];
        
        // ArrayList: Giống Wrapper Array + Overhead của cấu trúc danh sách
        ArrayList<Integer> list = new ArrayList<>(size);
        
        System.out.println("Primitive array là tối ưu nhất về Memory và Cache Locality.");
    }
}
```
**Độ phức tạp:** Space (như mô tả).

---

## 28. Partition Array (Quick Sort Partition)
**Đề bài chi tiết:** Phân chia (partition) một mảng `int[]` xung quanh một điểm chốt (pivot), sao cho các phần tử nhỏ hơn pivot nằm bên trái, và các phần tử lớn hơn hoặc bằng pivot nằm bên phải. Áp dụng sơ đồ phân chia Lomuto hoặc Hoare.
**Phân tích thuật toán:**
Sử dụng phân chia Lomuto: Chọn phần tử cuối cùng làm pivot. Duy trì một biến `i` theo dõi ranh giới của vùng các phần tử nhỏ hơn. Duyệt `j` qua mảng. Nếu phần tử tại `j` nhỏ hơn pivot, ta hoán đổi (swap) `arr[i]` và `arr[j]` rồi tăng `i`. Cuối cùng, hoán đổi pivot về đúng vị trí `i`.
**Mã nguồn Java:**
```java
public class PartitionArray {
    public static int partition(int[] arr, int low, int high) {
        int pivot = arr[high];
        int i = low;
        
        for (int j = low; j < high; j++) {
            if (arr[j] < pivot) {
                swap(arr, i, j);
                i++;
            }
        }
        swap(arr, i, high);
        return i;
    }
    
    private static void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }

    public static void main(String[] args) {
        int[] arr = {10, 80, 30, 90, 40, 50, 70};
        int p = partition(arr, 0, arr.length - 1);
        System.out.println("Pivot index: " + p); // 4
        System.out.println(java.util.Arrays.toString(arr)); // [10, 30, 40, 50, 80, 90, 70]
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 29. Rearrange Positive and Negative
**Đề bài chi tiết:** Cho mảng `int[] arr` gồm số lượng bằng nhau của số dương và số âm. Sắp xếp lại mảng in-place sao cho số dương và âm đứng xen kẽ nhau (Bắt đầu bằng số dương). Giữ nguyên thứ tự tương đối ban đầu của các số dương và âm, hoặc không cần (Tùy yêu cầu, bài này giả sử không cần giữ nguyên, để đạt Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$).
Nếu yêu cầu \textbf{không cần} in-place mà cần giữ thứ tự, có thể dùng mảng phụ. Dưới đây triển khai thuật toán không cần in-place, giữ nguyên thứ tự với Space $\mathcal{O}(N)$.
**Phân tích thuật toán:** Khởi tạo hai con trỏ `posIdx = 0` và `negIdx = 1`. Tạo mảng kết quả mới cùng kích thước. Duyệt qua mảng ban đầu, nếu là số dương thì đặt vào `posIdx` rồi tăng lên 2. Nếu là số âm thì đặt vào `negIdx` và tăng lên 2.
**Mã nguồn Java:**
```java
public class RearrangePosNeg {
    public static int[] rearrange(int[] arr) {
        int[] ans = new int[arr.length];
        int posIdx = 0, negIdx = 1;
        
        for (int num : arr) {
            if (num > 0) {
                if (posIdx < ans.length) {
                    ans[posIdx] = num;
                    posIdx += 2;
                }
            } else {
                if (negIdx < ans.length) {
                    ans[negIdx] = num;
                    negIdx += 2;
                }
            }
        }
        return ans;
    }

    public static void main(String[] args) {
        int[] arr = {3, 1, -2, -5, 2, -4};
        System.out.println(java.util.Arrays.toString(rearrange(arr)));
        // [3, -2, 1, -5, 2, -4]
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$. (Để làm với Space $\mathcal{O}(1)$ nhưng giữ nguyên thứ tự tương đối sẽ cần các phép dịch phải/trái tốn $\mathcal{O}(N^2)$ Time).

---

## 30. Maximum Product Subarray
**Đề bài chi tiết:** Cho mảng `int[] arr`, tìm mảng con liên tiếp không rỗng có tích lớn nhất và trả về tích đó.
**Phân tích thuật toán:** Giống như thuật toán Kadane cho mảng con có tổng lớn nhất, ta cũng duyệt qua mảng. Tuy nhiên, việc nhân hai số âm có thể tạo thành số dương lớn, nên ta cần theo dõi đồng thời giá trị tích lớn nhất (`maxProduct`) và nhỏ nhất (`minProduct`) tính đến thời điểm hiện tại. Tại mỗi bước, nếu số hiện tại là số âm, tích lớn nhất và nhỏ nhất sẽ hoán đổi giá trị cho nhau.
**Mã nguồn Java:**
```java
public class MaxProductSubarray {
    public static int maxProduct(int[] arr) {
        if (arr == null || arr.length == 0) return 0;
        
        int maxProductSoFar = arr[0];
        int minProductSoFar = arr[0];
        int result = arr[0];
        
        for (int i = 1; i < arr.length; i++) {
            int num = arr[i];
            
            if (num < 0) {
                // Hoán đổi max và min vì số âm đảo dấu
                int temp = maxProductSoFar;
                maxProductSoFar = minProductSoFar;
                minProductSoFar = temp;
            }
            
            maxProductSoFar = Math.max(num, maxProductSoFar * num);
            minProductSoFar = Math.min(num, minProductSoFar * num);
            
            result = Math.max(result, maxProductSoFar);
        }
        
        return result;
    }

    public static void main(String[] args) {
        int[] arr = {2, 3, -2, 4};
        System.out.println(maxProduct(arr)); // 6 (subarray [2, 3])
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.
