# 01. Các bài tập Cơ bản về Tìm kiếm nhị phân (Binary Search Basics Problems)

Tài liệu này bao gồm 30 bài tập ứng dụng Binary Search cơ bản, với 10 bài đầu tiên được giải chi tiết.

## 1. Binary Search (LeetCode 704)
**Đề bài chi tiết:** 
Cho một mảng số nguyên `nums` được sắp xếp theo thứ tự tăng dần và một số nguyên `target`. Viết hàm tìm kiếm `target` trong `nums`. Nếu `target` tồn tại, trả về chỉ số (index) của nó. Nếu không, trả về `-1`. Yêu cầu thuật toán phải có độ phức tạp thời gian $O(\log N)$.

**Phân tích thuật toán:** 
Sử dụng mẫu Binary Search chuẩn (Exact Match). Khởi tạo `left = 0` và `right = nums.length - 1`. Trong vòng lặp `while (left <= right)`, tính `mid`. Nếu tìm thấy `target`, trả về `mid`. Nếu `nums[mid] < target`, bỏ qua nửa trái (`left = mid + 1`), ngược lại bỏ nửa phải (`right = mid - 1`).

**Mã nguồn Java:**
```java
class Solution {
    public int search(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return -1;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(log N)
- Space Complexity: O(1)

---

## 2. Search Insert Position (LeetCode 35)
**Đề bài chi tiết:** 
Cho một mảng đã sắp xếp và một `target`, trả về index nếu tìm thấy `target`. Nếu không, trả về index nơi mà nó sẽ được chèn vào để mảng vẫn giữ nguyên thứ tự sắp xếp. Yêu cầu $O(\log N)$ time.

**Phân tích thuật toán:** 
Sử dụng Binary Search cơ bản. Nếu kết thúc vòng lặp mà không tìm thấy phần tử, con trỏ `left` sẽ luôn vượt qua `right` tại vị trí chèn lý tưởng đầu tiên lớn hơn `target`. Do đó, chỉ cần trả về `left` ở cuối hàm.

**Mã nguồn Java:**
```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) return mid;
            if (nums[mid] < target) left = mid + 1;
            else right = mid - 1;
        }
        return left;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(log N)
- Space Complexity: O(1)

---

## 3. Find First and Last Position of Element in Sorted Array (LeetCode 34)
**Đề bài chi tiết:** 
Cho mảng số nguyên `nums` sắp xếp tăng dần, tìm vị trí bắt đầu và kết thúc của phần tử `target`. Nếu không tìm thấy, trả về `[-1, -1]`. Yêu cầu thuật toán $O(\log N)$.

**Phân tích thuật toán:** 
Cần chạy 2 lần Binary Search. Lần 1 tìm Lower Bound (First Occurrence). Lần 2 tìm Upper Bound (Last Occurrence). Có thể tạo một hàm helper `findBound(nums, target, isFirst)` để tái sử dụng code.

**Mã nguồn Java:**
```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int[] result = new int[]{-1, -1};
        result[0] = findBound(nums, target, true);
        if (result[0] == -1) return result;
        result[1] = findBound(nums, target, false);
        return result;
    }

    private int findBound(int[] nums, int target, boolean isFirst) {
        int left = 0, right = nums.length - 1;
        int ans = -1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                ans = mid;
                if (isFirst) right = mid - 1; // Ép tiếp sang trái
                else left = mid + 1;         // Ép tiếp sang phải
            } else if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(log N)
- Space Complexity: O(1)

---

## 4. First Bad Version (LeetCode 278)
**Đề bài chi tiết:** 
Bạn là người quản lý sản phẩm. Sản phẩm mới nhất có bug ở phiên bản `bad`, làm cho các phiên bản sau nó cũng lỗi. Có API `isBadVersion(version)` trả về true nếu lỗi. Hãy tìm ra phiên bản lỗi đầu tiên (First Bad Version) ít lần gọi API nhất. Bạn có `n` phiên bản `[1, 2, ..., n]`.

**Phân tích thuật toán:** 
Đây là bài toán tìm First Occurrence của `true` trong mảng tưởng tượng `[false, false, ..., true, true]`. Ta tìm kiếm từ `left = 1` đến `right = n`. Nếu `isBadVersion(mid)` là `true`, ta lưu kết quả và di chuyển `right = mid - 1` để xem có lỗi nào trước đó không. Ngược lại `left = mid + 1`.

**Mã nguồn Java:**
```java
/* The isBadVersion API is defined in the parent class VersionControl.
      boolean isBadVersion(int version); */
public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
        int left = 1, right = n;
        int firstBad = 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (isBadVersion(mid)) {
                firstBad = mid;
                right = mid - 1; // Thử tìm cái lỗi trước đó
            } else {
                left = mid + 1;
            }
        }
        return firstBad;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(log N)
- Space Complexity: O(1)

---

## 5. Guess Number Higher or Lower (LeetCode 374)
**Đề bài chi tiết:** 
Ta chơi trò đoán số từ `1` đến `n`. API `guess(num)` trả về:
- `-1`: Số bạn đoán lớn hơn số của tôi (num > pick).
- `1`: Số bạn đoán nhỏ hơn số của tôi (num < pick).
- `0`: Bạn đoán đúng.
Hãy tìm ra số `pick`.

**Phân tích thuật toán:** 
Áp dụng Binary Search trực tiếp trên đoạn `[1, n]`. So sánh thông qua API `guess(mid)`.

**Mã nguồn Java:**
```java
/** 
 * Forward declaration of guess API.
 * @param  num   your guess
 * @return 	     -1 if num is higher than the picked number
 *			      1 if num is lower than the picked number
 *               otherwise return 0
 * int guess(int num);
 */
public class Solution extends GuessGame {
    public int guessNumber(int n) {
        int left = 1, right = n;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int res = guess(mid);
            if (res == 0) return mid;
            else if (res < 0) right = mid - 1; // Đoán cao quá
            else left = mid + 1;               // Đoán thấp quá
        }
        return -1;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(log N)
- Space Complexity: O(1)

---

## 6. Valid Perfect Square (LeetCode 367)
**Đề bài chi tiết:** 
Cho số nguyên dương `num`. Viết hàm trả về `true` nếu `num` là một số chính phương hoàn hảo (perfect square), ngược lại trả về `false`. Không sử dụng hàm thư viện như `sqrt`.

**Phân tích thuật toán:** 
Không gian tìm kiếm là `[1, num]`. Với mỗi `mid`, ta kiểm tra `mid * mid == num`. Để tránh Integer Overflow, dùng biến kiểu `long` cho `mid` và bình phương của nó.

**Mã nguồn Java:**
```java
class Solution {
    public boolean isPerfectSquare(int num) {
        if (num < 1) return false;
        long left = 1, right = num;
        while (left <= right) {
            long mid = left + (right - left) / 2;
            long square = mid * mid;
            if (square == num) return true;
            else if (square > num) right = (int) (mid - 1);
            else left = (int) (mid + 1);
        }
        return false;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(log N)
- Space Complexity: O(1)

---

## 7. Sqrt(x) (LeetCode 69)
**Đề bài chi tiết:** 
Tính và trả về căn bậc hai nguyên của `x`, trong đó `x` là số không âm. Làm tròn xuống tới số nguyên gần nhất.

**Phân tích thuật toán:** 
Tương tự bài Valid Perfect Square. Tìm số nguyên `ans` lớn nhất sao cho `ans * ans <= x`. Ta duyệt tìm kiếm nhị phân, lưu kết quả hợp lệ lớn nhất. 

**Mã nguồn Java:**
```java
class Solution {
    public int mySqrt(int x) {
        if (x == 0) return 0;
        int left = 1, right = x;
        int ans = 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            // Dùng chia để tránh tràn số: mid <= x / mid thay vì mid * mid <= x
            if (mid <= x / mid) {
                ans = mid; // Có thể là đáp án, lưu lại và tăng left
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(log N)
- Space Complexity: O(1)

---

## 8. Arranging Coins (LeetCode 441)
**Đề bài chi tiết:** 
Bạn có `n` đồng xu, muốn xếp chúng thành các bậc thang. Bậc thứ `k` sẽ có đúng `k` đồng xu. Bậc thang cuối cùng có thể không đầy đủ. Tính tổng số bậc thang đầy đủ (hoàn chỉnh) xếp được.

**Phân tích thuật toán:** 
Tổng số xu để hoàn thành `k` bậc là $\frac{k(k+1)}{2}$. Tìm giá trị `k` lớn nhất sao cho $\frac{k(k+1)}{2} \le n$. Dùng Binary Search với `left = 1` và `right = n`. Cần dùng `long` để tránh tràn.

**Mã nguồn Java:**
```java
class Solution {
    public int arrangeCoins(int n) {
        long left = 1, right = n;
        long ans = 0;
        while (left <= right) {
            long mid = left + (right - left) / 2;
            long coinsNeeded = mid * (mid + 1) / 2;
            if (coinsNeeded <= n) {
                ans = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return (int) ans;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(log N)
- Space Complexity: O(1)

---

## 9. Kth Missing Positive Number (LeetCode 1539)
**Đề bài chi tiết:** 
Cho một mảng `arr` chứa các số nguyên dương tăng dần nghiêm ngặt (strictly increasing), và một số nguyên `k`. Tìm số nguyên dương thứ `k` bị thiếu trong mảng.

**Phân tích thuật toán:** 
Tại index `i`, số lượng các phần tử dương đã bị thiếu tính từ $1$ đến `arr[i]` là: `arr[i] - (i + 1)`. 
Dùng Binary Search để tìm index nơi số lượng phần tử thiếu bắt đầu $\ge k$. Khi vòng lặp kết thúc, `left` trỏ tới điểm đó. Kết quả số bị thiếu sẽ là `left + k`.

**Mã nguồn Java:**
```java
class Solution {
    public int findKthPositive(int[] arr, int k) {
        int left = 0, right = arr.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int missingCount = arr[mid] - (mid + 1);
            if (missingCount < k) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return left + k;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(log N)
- Space Complexity: O(1)

---

## 10. Find Smallest Letter Greater Than Target (LeetCode 744)
**Đề bài chi tiết:** 
Cho một mảng các ký tự `letters` đã được sắp xếp tăng dần và ký tự `target`. Tìm ký tự nhỏ nhất trong mảng lớn hơn `target` theo thứ tự từ vựng. Cấu trúc mảng quay vòng, nếu không có ký tự lớn hơn, trả về ký tự đầu tiên.

**Phân tích thuật toán:** 
Dùng Binary Search tìm phần tử đầu tiên lớn hơn `target` (Upper Bound khắt khe). Nếu vòng lặp chạy hết mà không thấy (khi `target` lớn hơn hoặc bằng ký tự cuối), `left` sẽ bằng `letters.length`, ta dùng phép toán `%` để trả về ký tự đầu.

**Mã nguồn Java:**
```java
class Solution {
    public char nextGreatestLetter(char[] letters, char target) {
        int left = 0, right = letters.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (letters[mid] <= target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return letters[left % letters.length];
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(log N)
- Space Complexity: O(1)

---

## 11. Two Sum II - Input Array Is Sorted (LeetCode 167)
**Đề bài chi tiết:**
Cho một mảng số nguyên `numbers` đã được sắp xếp theo thứ tự tăng dần và một số nguyên `target`. Tìm hai số trong mảng sao cho tổng của chúng bằng `target`. Trả về chỉ số (index) của hai số đó (chỉ số bắt đầu từ 1). Không sử dụng không gian nhớ bổ sung ngoài $O(1)$ và mỗi phần tử chỉ được sử dụng một lần.

**Phân tích thuật toán:**
Mặc dù bài toán này thường được giải bằng kỹ thuật Two Pointers tối ưu với độ phức tạp $O(N)$, ta cũng có thể sử dụng Binary Search. Với mỗi phần tử `numbers[i]`, ta dùng Binary Search để tìm phần tử có giá trị `target - numbers[i]` trong mảng con từ chỉ số `i + 1` đến cuối mảng.

**Mã nguồn Java:**
```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        for (int i = 0; i < numbers.length; i++) {
            int left = i + 1, right = numbers.length - 1;
            int expected = target - numbers[i];
            while (left <= right) {
                int mid = left + (right - left) / 2;
                if (numbers[mid] == expected) {
                    return new int[]{i + 1, mid + 1};
                } else if (numbers[mid] < expected) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
        }
        return new int[]{-1, -1};
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(N log N) (Nếu dùng Two Pointers sẽ là O(N))
- Space Complexity: O(1)

---

## 12. Peak Index in a Mountain Array (LeetCode 852)
**Đề bài chi tiết:**
Cho một mảng `arr` có tính chất "núi" (mountain array), nghĩa là mảng tăng dần liên tục đến một đỉnh rồi giảm dần liên tục. Tìm chỉ số (index) của đỉnh núi đó. Yêu cầu thuật toán phải có độ phức tạp thời gian là $O(\log N)$.

**Phân tích thuật toán:**
Đỉnh núi là phần tử duy nhất lớn hơn cả phần tử trước và sau nó. Áp dụng Binary Search bằng cách so sánh `arr[mid]` và `arr[mid + 1]`. Nếu `arr[mid] < arr[mid + 1]`, ta đang ở sườn núi đi lên, đỉnh núi chắc chắn nằm bên phải nên `left = mid + 1`. Ngược lại, nếu `arr[mid] >= arr[mid + 1]`, ta đang ở sườn núi đi xuống hoặc ngay tại đỉnh, nên `right = mid`.

**Mã nguồn Java:**
```java
class Solution {
    public int peakIndexInMountainArray(int[] arr) {
        int left = 0, right = arr.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (arr[mid] < arr[mid + 1]) {
                left = mid + 1; // Sườn núi đi lên
            } else {
                right = mid; // Sườn núi đi xuống hoặc đỉnh
            }
        }
        return left;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(log N)
- Space Complexity: O(1)

---

## 13. Find Minimum in Rotated Sorted Array (LeetCode 153)
**Đề bài chi tiết:**
Cho mảng `nums` chứa các giá trị duy nhất, ban đầu được sắp xếp tăng dần nhưng đã bị xoay (rotated) tại một điểm chưa biết trước. Tìm và trả về phần tử nhỏ nhất trong mảng. Yêu cầu độ phức tạp $O(\log N)$.

**Phân tích thuật toán:**
Do mảng đã bị xoay, ta có thể dùng Binary Search. Ta so sánh phần tử ở `mid` với phần tử ở `right`. Nếu `nums[mid] > nums[right]`, phần nhỏ nhất chắc chắn nằm ở nửa phải (bỏ qua nửa trái bằng `left = mid + 1`). Ngược lại, nếu `nums[mid] <= nums[right]`, phần nhỏ nhất nằm ở nửa trái hoặc chính là `mid` (cập nhật `right = mid`).

**Mã nguồn Java:**
```java
class Solution {
    public int findMin(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[right]) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return nums[left];
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(log N)
- Space Complexity: O(1)

---

## 14. Find Minimum in Rotated Sorted Array II (LeetCode 154)
**Đề bài chi tiết:**
Tương tự bài 13, nhưng mảng `nums` có thể chứa các giá trị trùng lặp. Tìm và trả về phần tử nhỏ nhất trong mảng xoay vòng này.

**Phân tích thuật toán:**
Khi có phần tử trùng lặp, trường hợp `nums[mid] == nums[right]` không thể xác định phần nhỏ nhất nằm ở nửa nào (ví dụ: `[3, 3, 1, 3]`). Tuy nhiên, vì `nums[mid] == nums[right]`, ta có thể an toàn giảm `right` đi 1 (`right--`) để loại bỏ bớt phần tử thừa mà không làm mất đi giá trị nhỏ nhất (bởi vì giá trị đó vẫn còn ở `mid`). Các trường hợp khác xử lý giống bài 13.

**Mã nguồn Java:**
```java
class Solution {
    public int findMin(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[right]) {
                left = mid + 1;
            } else if (nums[mid] < nums[right]) {
                right = mid;
            } else {
                right--; // Loại bỏ phần tử trùng lặp ở cuối
            }
        }
        return nums[left];
    }
}
```
**Độ phức tạp:**
- Time Complexity: Trung bình O(log N), trường hợp xấu nhất O(N) khi các phần tử đều giống nhau.
- Space Complexity: O(1)

---

## 15. Search in Rotated Sorted Array (LeetCode 33)
**Đề bài chi tiết:**
Cho mảng `nums` ban đầu được sắp xếp tăng dần, không chứa phần tử trùng lặp và đã bị xoay. Cho số nguyên `target`. Nếu tìm thấy `target`, trả về chỉ số của nó, nếu không tìm thấy trả về `-1`. Yêu cầu thuật toán $O(\log N)$.

**Phân tích thuật toán:**
Tại mỗi bước Binary Search, chia đôi mảng sẽ luôn có ít nhất một nửa được sắp xếp đúng thứ tự.
Nếu nửa trái `[left, mid]` được sắp xếp (`nums[left] <= nums[mid]`): kiểm tra xem `target` có nằm trong khoảng này không. Nếu có, tìm tiếp ở nửa trái, ngược lại tìm ở nửa phải.
Nếu nửa phải `[mid, right]` được sắp xếp: kiểm tra xem `target` có nằm trong khoảng này không. Nếu có, tìm tiếp ở nửa phải, ngược lại tìm ở nửa trái.

**Mã nguồn Java:**
```java
class Solution {
    public int search(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) return mid;
            
            // Nửa trái được sắp xếp
            if (nums[left] <= nums[mid]) {
                if (target >= nums[left] && target < nums[mid]) {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            } 
            // Nửa phải được sắp xếp
            else {
                if (target > nums[mid] && target <= nums[right]) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
        }
        return -1;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(log N)
- Space Complexity: O(1)

---

## 16. Search in Rotated Sorted Array II (LeetCode 81)
**Đề bài chi tiết:**
Mở rộng của bài 15, mảng `nums` bị xoay nhưng có thể chứa các phần tử trùng lặp. Viết hàm trả về `true` nếu `target` tồn tại trong mảng, ngược lại trả về `false`.

**Phân tích thuật toán:**
Do có phần tử trùng lặp, khi `nums[left] == nums[mid] == nums[right]`, ta không thể biết nửa nào được sắp xếp. Giải pháp là thu hẹp khoảng tìm kiếm bằng cách di chuyển `left++` và `right--` để loại bỏ các phần tử gây nhầm lẫn này. Phần còn lại của thuật toán giữ nguyên như bài 15.

**Mã nguồn Java:**
```java
class Solution {
    public boolean search(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) return true;
            
            // Không thể xác định nửa nào được sắp xếp do trùng lặp
            if (nums[left] == nums[mid] && nums[mid] == nums[right]) {
                left++;
                right--;
                continue;
            }
            
            // Nửa trái được sắp xếp
            if (nums[left] <= nums[mid]) {
                if (target >= nums[left] && target < nums[mid]) {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            } 
            // Nửa phải được sắp xếp
            else {
                if (target > nums[mid] && target <= nums[right]) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
        }
        return false;
    }
}
```
**Độ phức tạp:**
- Time Complexity: Trung bình O(log N), trường hợp xấu nhất O(N).
- Space Complexity: O(1)

---

## 17. Find Peak Element (LeetCode 162)
**Đề bài chi tiết:**
Phần tử đỉnh (peak element) là phần tử lớn hơn nghiêm ngặt so với các phần tử lân cận của nó. Cho một mảng `nums`, tìm đỉnh và trả về chỉ số của nó. Nếu mảng có nhiều đỉnh, trả về bất kỳ đỉnh nào. Giả sử `nums[-1] = nums[n] = -∞`. Cần đạt độ phức tạp $O(\log N)$.

**Phân tích thuật toán:**
Sử dụng Binary Search dựa trên hướng của dốc (slope). So sánh `nums[mid]` với `nums[mid + 1]`. Nếu `nums[mid] < nums[mid + 1]`, mảng đang đi lên, chắc chắn có đỉnh ở bên phải, ta gán `left = mid + 1`. Nếu `nums[mid] > nums[mid + 1]`, mảng đang đi xuống, có đỉnh ở bên trái (có thể là chính `mid`), ta gán `right = mid`.

**Mã nguồn Java:**
```java
class Solution {
    public int findPeakElement(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < nums[mid + 1]) {
                left = mid + 1; // Dốc lên
            } else {
                right = mid;    // Dốc xuống
            }
        }
        return left;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(log N)
- Space Complexity: O(1)

---

## 18. Count Negative Numbers in a Sorted Matrix (LeetCode 1351)
**Đề bài chi tiết:**
Cho một ma trận `grid` kích thước `m x n` được sắp xếp giảm dần theo cả hàng và cột. Đếm số lượng các số âm trong ma trận.

**Phân tích thuật toán:**
Duyệt qua từng hàng. Mỗi hàng được sắp xếp giảm dần, nên ta dùng Binary Search để tìm phần tử âm đầu tiên trong hàng đó. Nếu phần tử âm đầu tiên nằm ở vị trí `index`, số lượng số âm trong hàng đó sẽ là `n - index`.

**Mã nguồn Java:**
```java
class Solution {
    public int countNegatives(int[][] grid) {
        int count = 0;
        for (int[] row : grid) {
            int left = 0, right = row.length - 1;
            int firstNegativeIndex = row.length;
            while (left <= right) {
                int mid = left + (right - left) / 2;
                if (row[mid] < 0) {
                    firstNegativeIndex = mid;
                    right = mid - 1; // Tiếp tục tìm về bên trái
                } else {
                    left = mid + 1;
                }
            }
            count += (row.length - firstNegativeIndex);
        }
        return count;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(M log N), với M là số lượng hàng và N là số lượng cột.
- Space Complexity: O(1)

---

## 19. Koko Eating Bananas (LeetCode 875)
**Đề bài chi tiết:**
Koko có `n` nải chuối (mảng `piles`), Koko cần ăn hết chuối trong `h` giờ. Koko có thể quyết định tốc độ ăn chuối của mình là `k` quả/giờ. Mỗi giờ cô chọn một nải và ăn tối đa `k` quả từ nải đó. Nếu nải có ít hơn `k` quả, cô ăn hết và nghỉ trong thời gian còn lại của giờ đó. Tìm tốc độ ăn tối thiểu `k` sao cho Koko có thể ăn hết số chuối trong `h` giờ.

**Phân tích thuật toán:**
Đây là bài toán Binary Search trên tập kết quả (Binary Search on Answer). Tốc độ ăn tối thiểu có thể là 1, và tốc độ tối đa cần thiết không quá `max(piles)`. Ta tìm kiếm tốc độ `mid`, tính tổng số giờ cần thiết để ăn hết chuối với tốc độ này. Nếu tổng thời gian `> h`, tốc độ quá chậm, ta cần tăng `k` (`left = mid + 1`). Nếu tổng thời gian `<= h`, tốc độ đủ nhanh, ta thử tìm tốc độ chậm hơn (`right = mid - 1`).

**Mã nguồn Java:**
```java
class Solution {
    public int minEatingSpeed(int[] piles, int h) {
        int left = 1, right = 1;
        for (int pile : piles) {
            right = Math.max(right, pile);
        }
        
        int ans = right;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            long hours = 0;
            for (int pile : piles) {
                // Tương đương với Math.ceil((double) pile / mid)
                hours += (pile + mid - 1) / mid;
            }
            
            if (hours <= h) {
                ans = mid;        // Thỏa mãn, lưu lại và thử tốc độ nhỏ hơn
                right = mid - 1;
            } else {
                left = mid + 1;   // Không thỏa mãn, cần tăng tốc độ
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(N log(Max(P))), với N là số lượng nải chuối và P là số lượng quả lớn nhất trong một nải.
- Space Complexity: O(1)

---

## 20. Capacity To Ship Packages Within D Days (LeetCode 1011)
**Đề bài chi tiết:**
Một băng chuyền cần vận chuyển các gói hàng (mảng `weights`) trong `days` ngày. Các gói hàng phải được vận chuyển theo đúng thứ tự mảng ban đầu. Tìm sức chứa tối thiểu của tàu sao cho có thể vận chuyển hết tất cả các gói hàng trong `days` ngày.

**Phân tích thuật toán:**
Tương tự bài 19, ta dùng Binary Search trên tập kết quả. Sức chứa tối thiểu `left` không thể nhỏ hơn kiện hàng nặng nhất (`max(weights)`), vì nếu không thì không thể chở nổi kiện hàng đó. Sức chứa tối đa `right` là tổng khối lượng tất cả các kiện (có thể chở tất cả trong một chuyến). Ta thử mức sức chứa `mid`, đếm số ngày cần thiết. Nếu số ngày `> days`, cần sức chứa lớn hơn (`left = mid + 1`). Nếu số ngày `<= days`, tàu có thể chở được, ta ghi nhận kết quả và thử tìm sức chứa nhỏ hơn (`right = mid - 1`).

**Mã nguồn Java:**
```java
class Solution {
    public int shipWithinDays(int[] weights, int days) {
        int left = 0, right = 0;
        for (int w : weights) {
            left = Math.max(left, w);
            right += w;
        }
        
        int ans = right;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int currentDays = 1, currentWeight = 0;
            
            for (int w : weights) {
                if (currentWeight + w > mid) {
                    currentDays++;
                    currentWeight = w;
                } else {
                    currentWeight += w;
                }
            }
            
            if (currentDays <= days) {
                ans = mid;        // Thỏa mãn, lưu lại và thử giảm sức chứa
                right = mid - 1;
            } else {
                left = mid + 1;   // Không thỏa mãn, cần tăng sức chứa
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(N log(Sum(W) - Max(W))), với N là số lượng gói hàng và W là trọng lượng các gói.
- Space Complexity: O(1)

---

## 21. Minimum Number of Days to Make m Bouquets (LeetCode 1482)
**Đề bài chi tiết:**
Cho một mảng số nguyên `bloomDay` trong đó `bloomDay[i]` là ngày mà bông hoa thứ `i` sẽ nở. Bạn cần tạo ra `m` bó hoa. Để làm một bó hoa, bạn cần sử dụng `k` bông hoa liền kề nhau từ khu vườn. Tìm số ngày tối thiểu bạn cần đợi để có thể làm được `m` bó hoa. Nếu không thể tạo đủ số bó hoa, trả về `-1`.

**Phân tích thuật toán:**
Đây là bài toán Binary Search on Answer. Nếu `m * k > bloomDay.length`, chắc chắn không thể làm đủ, trả về `-1`.
Không gian tìm kiếm số ngày là từ `min(bloomDay)` đến `max(bloomDay)`. Tại mỗi số ngày `mid`, ta kiểm tra xem có thể làm được bao nhiêu bó hoa bằng cách đếm các bông hoa liên tiếp đã nở (`bloomDay[i] <= mid`). Khi thu được `k` bông liên tiếp, ta có 1 bó hoa và đếm lại từ đầu. Nếu tổng số bó hoa `>= m`, số ngày này thỏa mãn, ta tiếp tục tìm số ngày nhỏ hơn (`right = mid - 1`). Ngược lại, cần chờ nhiều ngày hơn (`left = mid + 1`).

**Mã nguồn Java:**
```java
class Solution {
    public int minDays(int[] bloomDay, int m, int k) {
        int n = bloomDay.length;
        // Kiểm tra tránh tràn số khi nhân m với k
        if ((long) m * k > n) return -1;
        
        int left = Integer.MAX_VALUE, right = Integer.MIN_VALUE;
        for (int day : bloomDay) {
            left = Math.min(left, day);
            right = Math.max(right, day);
        }
        
        int ans = -1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int bouquets = 0, flowers = 0;
            
            for (int day : bloomDay) {
                if (day <= mid) {
                    flowers++;
                    if (flowers == k) {
                        bouquets++;
                        flowers = 0; // Reset để đếm bó mới
                    }
                } else {
                    flowers = 0; // Bị gián đoạn bởi hoa chưa nở
                }
            }
            
            if (bouquets >= m) {
                ans = mid;
                right = mid - 1; // Tìm số ngày ít hơn
            } else {
                left = mid + 1;  // Cần thêm ngày
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(N log(Max(D))), với D là giá trị lớn nhất trong `bloomDay`.
- Space Complexity: O(1)

---

## 22. Split Array Largest Sum (LeetCode 410)
**Đề bài chi tiết:**
Cho một mảng số nguyên `nums` không âm và số nguyên `k`. Chia mảng thành `k` mảng con không rỗng liên tiếp sao cho tổng lớn nhất trong các mảng con đó là nhỏ nhất có thể. Trả về tổng lớn nhất đó.

**Phân tích thuật toán:**
Dùng Binary Search trên tập kết quả. Kết quả `ans` nhỏ nhất không thể nhỏ hơn phần tử lớn nhất trong mảng (`max(nums)`), vì mỗi phần tử ít nhất phải nằm trong 1 mảng con. Kết quả lớn nhất có thể là tổng tất cả các phần tử (`sum(nums)`) khi `k=1`. Thử với một giá trị `mid`, ta duyệt mảng và cộng dồn tổng vào mảng con hiện tại. Nếu cộng thêm phần tử tiếp theo làm tổng vượt qua `mid`, ta phải tách sang mảng con mới. Nếu số lượng mảng con cần thiết `<= k`, giá trị `mid` là hợp lệ, ta thử tìm giá trị nhỏ hơn. Ngược lại, ta phải tăng `mid`.

**Mã nguồn Java:**
```java
class Solution {
    public int splitArray(int[] nums, int k) {
        int left = 0, right = 0;
        for (int num : nums) {
            left = Math.max(left, num);
            right += num;
        }
        
        int ans = right;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int subarrays = 1, currentSum = 0;
            
            for (int num : nums) {
                if (currentSum + num > mid) {
                    subarrays++;
                    currentSum = num;
                } else {
                    currentSum += num;
                }
            }
            
            if (subarrays <= k) {
                ans = mid;
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(N log(Sum - Max)), với N là kích thước mảng.
- Space Complexity: O(1)

---

## 23. Find Right Interval (LeetCode 436)
**Đề bài chi tiết:**
Cho một tập hợp các khoảng `intervals`, trong đó `intervals[i] = [start_i, end_i]`. 
Với mỗi khoảng `i`, khoảng bên phải (right interval) của nó là khoảng `j` có `start_j >= end_i` và `start_j` là nhỏ nhất có thể. Trả về mảng chỉ số của khoảng bên phải tương ứng cho mỗi khoảng. Nếu không có, trả về `-1`.

**Phân tích thuật toán:**
Trích xuất điểm bắt đầu (`start`) và chỉ số ban đầu của mỗi khoảng ra một mảng cấu trúc rồi sắp xếp theo `start`. Đối với mỗi khoảng, lấy điểm kết thúc (`end`) của nó và sử dụng Binary Search trên mảng điểm bắt đầu đã sắp xếp để tìm giá trị `start` đầu tiên lớn hơn hoặc bằng `end` (tìm Lower Bound).

**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int[] findRightInterval(int[][] intervals) {
        int n = intervals.length;
        int[][] starts = new int[n][2];
        for (int i = 0; i < n; i++) {
            starts[i][0] = intervals[i][0];
            starts[i][1] = i; // Lưu lại index ban đầu
        }
        
        Arrays.sort(starts, (a, b) -> Integer.compare(a[0], b[0]));
        int[] result = new int[n];
        
        for (int i = 0; i < n; i++) {
            int target = intervals[i][1];
            int left = 0, right = n - 1;
            int ans = -1;
            
            while (left <= right) {
                int mid = left + (right - left) / 2;
                if (starts[mid][0] >= target) {
                    ans = starts[mid][1];
                    right = mid - 1; // Ép trái để lấy start nhỏ nhất
                } else {
                    left = mid + 1;
                }
            }
            result[i] = ans;
        }
        return result;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(N log N) do phải sắp xếp và thực hiện N lần Binary Search.
- Space Complexity: O(N) để lưu mảng `starts`.

---

## 24. Longest Increasing Subsequence (LeetCode 300)
**Đề bài chi tiết:**
Cho một mảng số nguyên `nums`, tìm độ dài của dãy con tăng ngặt dài nhất (Longest Increasing Subsequence - LIS). Dãy con là dãy tạo từ việc xóa đi một số phần tử mà không làm thay đổi thứ tự phần tử còn lại.

**Phân tích thuật toán:**
Tạo một mảng phụ `tails` (có thể dùng chính mảng mới để mô phỏng), trong đó `tails[i]` lưu phần tử nhỏ nhất của phần đuôi của một dãy con tăng có độ dài `i + 1`. Mảng `tails` này luôn có thứ tự tăng dần.
Duyệt qua các phần tử của `nums`. Nếu `nums[i]` lớn hơn phần tử cuối cùng của `tails`, ta nối nó vào cuối (tăng độ dài LIS). Nếu không, dùng Binary Search tìm vị trí đầu tiên trong `tails` có giá trị `>= nums[i]` và thay thế nó bằng `nums[i]`. Việc này giúp dãy có khả năng dễ dàng nối thêm các phần tử sau này hơn.

**Mã nguồn Java:**
```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int[] tails = new int[nums.length];
        int size = 0;
        
        for (int x : nums) {
            int left = 0, right = size - 1;
            while (left <= right) {
                int mid = left + (right - left) / 2;
                if (tails[mid] < x) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
            // Thay thế bằng x hoặc thêm mới ở vị trí left
            tails[left] = x;
            if (left == size) {
                size++;
            }
        }
        return size;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(N log N)
- Space Complexity: O(N) cho mảng `tails`

---

## 25. Single Element in a Sorted Array (LeetCode 540)
**Đề bài chi tiết:**
Bạn được cung cấp một mảng số nguyên `nums` đã được sắp xếp, trong đó mỗi phần tử xuất hiện đúng hai lần, ngoại trừ một phần tử xuất hiện một lần duy nhất. Tìm phần tử đơn độc đó. Thuật toán của bạn phải chạy trong độ phức tạp thời gian $O(\log N)$ và $O(1)$ không gian.

**Phân tích thuật toán:**
Số lượng phần tử trong mảng luôn là số lẻ. Các phần tử giống nhau tạo thành các cặp. 
Trước phần tử đơn độc: cặp phần tử bằng nhau nằm ở index chẵn rồi đến lẻ `(chẵn, lẻ)`. 
Sau phần tử đơn độc: trật tự thay đổi thành `(lẻ, chẵn)` do bị chèn vào giữa một phần tử duy nhất.
Dùng Binary Search đảm bảo `mid` luôn là số chẵn. Nếu `nums[mid] == nums[mid + 1]`, điều này chỉ xảy ra ở nửa mảng trước khi có phần tử đơn, nên phần tử đơn nằm ở nửa bên phải (`left = mid + 2`). Ngược lại, phần tử đơn nằm ở nửa bên trái kể cả chính `mid` (`right = mid`).

**Mã nguồn Java:**
```java
class Solution {
    public int singleNonDuplicate(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            
            // Đảm bảo mid là index chẵn
            if (mid % 2 == 1) {
                mid--;
            }
            
            // Nếu đúng thứ tự (chẵn, lẻ) giống nhau, phần tử đơn bên phải
            if (nums[mid] == nums[mid + 1]) {
                left = mid + 2;
            } else {
                right = mid;
            }
        }
        return nums[left];
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(log N)
- Space Complexity: O(1)

---

## 26. Intersection of Two Arrays (LeetCode 349)
**Đề bài chi tiết:**
Cho hai mảng số nguyên `nums1` và `nums2`, trả về mảng giao (intersection) của chúng. Mỗi phần tử trong kết quả phải là duy nhất. 

**Phân tích thuật toán:**
Mặc dù có thể sử dụng HashSet với độ phức tạp $O(M + N)$, để ứng dụng Binary Search, ta sắp xếp `nums1`. Sau đó tạo một tập Set lưu kết quả để tránh trùng lặp. Duyệt qua từng phần tử của `nums2` và thực hiện Binary Search trên `nums1`. Nếu tìm thấy, thêm vào tập kết quả.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Arrays.sort(nums1);
        Set<Integer> resultSet = new HashSet<>();
        
        for (int target : nums2) {
            if (binarySearch(nums1, target)) {
                resultSet.add(target);
            }
        }
        
        int[] result = new int[resultSet.size()];
        int i = 0;
        for (int num : resultSet) {
            result[i++] = num;
        }
        return result;
    }
    
    private boolean binarySearch(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) return true;
            else if (nums[mid] < target) left = mid + 1;
            else right = mid - 1;
        }
        return false;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(M log M + N log M), với M là độ dài `nums1`, N là độ dài `nums2`.
- Space Complexity: O(min(M, N)) cho tập lưu kết quả.

---

## 27. Intersection of Two Arrays II (LeetCode 350)
**Đề bài chi tiết:**
Tương tự bài 26, nhưng mỗi phần tử trong mảng kết quả phải xuất hiện số lần tương ứng bằng đúng với số lần nó xuất hiện chung trong cả hai mảng (cho phép trùng lặp).

**Phân tích thuật toán:**
Mặc dù Two Pointers trên 2 mảng sắp xếp là lý tưởng hơn, ta vẫn có thể dùng Binary Search bằng cách: sắp xếp `nums1`. Với mỗi phần tử trong `nums2`, dùng Binary Search tìm `Lower Bound` của nó trong `nums1` bắt đầu từ chỉ số `left = 0`. Tuy nhiên để đánh dấu phần tử đã dùng, ta cần một mảng boolean đánh dấu hoặc tăng `left` ranh giới tìm kiếm nếu mảng cũng được duyệt tuần tự. Cách đơn giản bằng BS cho Follow Up là sắp xếp cả hai, duyệt `nums2` và thu hẹp dần `left` của `nums1` để tránh ghép cặp lại vào phần tử trước đó.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        Arrays.sort(nums1); // Mảng gốc
        Arrays.sort(nums2); // Sắp xếp mảng kia để tìm tịnh tiến
        
        List<Integer> list = new ArrayList<>();
        int leftBound = 0;
        
        for (int target : nums2) {
            int left = leftBound, right = nums1.length - 1;
            int foundIdx = -1;
            
            // Tìm First Occurrence
            while (left <= right) {
                int mid = left + (right - left) / 2;
                if (nums1[mid] == target) {
                    foundIdx = mid;
                    right = mid - 1; // Ép trái để tìm phần tử đầu tiên
                } else if (nums1[mid] < target) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
            
            if (foundIdx != -1) {
                list.add(target);
                leftBound = foundIdx + 1; // Cập nhật ranh giới để không dùng lại
            }
        }
        
        int[] res = new int[list.size()];
        for (int i = 0; i < list.size(); i++) res[i] = list.get(i);
        return res;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(M log M + N log N + N log M) do chi phí sắp xếp và Binary Search.
- Space Complexity: O(min(M, N)) (không tính không gian bộ nhớ lưu kết quả trả về).

---

## 28. Heaters (LeetCode 475)
**Đề bài chi tiết:**
Trên một trục 1D có các ngôi nhà (mảng `houses`) và các lò sưởi (mảng `heaters`). Mỗi lò sưởi tỏa nhiệt theo bán kính `r`. Tìm bán kính tối thiểu `r` để mọi ngôi nhà đều nằm trong phạm vi sưởi ấm của ít nhất một lò sưởi.

**Phân tích thuật toán:**
Để mọi nhà đều ấm, ta cần tìm cho mỗi ngôi nhà một lò sưởi gần nó nhất. Khoảng cách đó chính là lượng bán kính tối thiểu nó cần. Lấy max của các khoảng cách này sẽ ra bán kính tối thiểu cho cả hệ thống.
Sắp xếp mảng `heaters`. Với mỗi phần tử trong `houses`, sử dụng Binary Search để tìm lò sưởi gần nhất (nằm bên trái hoặc bên phải). Bằng cách tìm phần tử có giá trị gần nhất qua Binary Search, rồi tính `min(khoảng cách trái, khoảng cách phải)`.

**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int findRadius(int[] houses, int[] heaters) {
        Arrays.sort(heaters);
        int maxRadius = 0;
        
        for (int house : houses) {
            int left = 0, right = heaters.length - 1;
            int nearestDist = Integer.MAX_VALUE;
            
            while (left <= right) {
                int mid = left + (right - left) / 2;
                nearestDist = Math.min(nearestDist, Math.abs(heaters[mid] - house));
                
                if (heaters[mid] == house) {
                    nearestDist = 0;
                    break; // Trùng ngay tại nhà
                } else if (heaters[mid] < house) {
                    left = mid + 1; // Lò sưởi đang ở bên trái, tìm qua phải xem có gần hơn không
                } else {
                    right = mid - 1; // Lò sưởi đang ở bên phải, tìm qua trái
                }
            }
            maxRadius = Math.max(maxRadius, nearestDist);
        }
        
        return maxRadius;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(K log K + N log K), với N là số ngôi nhà, K là số lò sưởi.
- Space Complexity: O(1) hoặc O(log K) khi tính hệ số không gian của thư viện sắp xếp.

---

## 29. H-Index II (LeetCode 275)
**Đề bài chi tiết:**
Chỉ số h-index của một nhà khoa học là `h` khi nhà khoa học đó có ít nhất `h` bài báo được trích dẫn (citations) `>= h` lần. Cho một mảng `citations` chứa số lượng trích dẫn của các bài báo đã được sắp xếp tăng dần, tính h-index của nhà khoa học. Phải có độ phức tạp $O(\log N)$.

**Phân tích thuật toán:**
Số lượng bài báo có lượt trích dẫn từ `citations[mid]` trở lên là `n - mid`.
Nếu `citations[mid] >= n - mid`, tức là có `n - mid` bài báo được trích dẫn ít nhất `n - mid` lần. Đây là một h-index hợp lệ. Do mảng sắp xếp tăng, chỉ số `n - mid` càng lớn khi `mid` càng nhỏ. Vì vậy ta cần tìm `mid` nhỏ nhất thỏa mãn, bằng cách gán `right = mid - 1`. Ngược lại, nếu chưa thỏa mãn, phải gán `left = mid + 1`.
Kết quả h-index lớn nhất sẽ là `n - left` khi kết thúc vòng lặp.

**Mã nguồn Java:**
```java
class Solution {
    public int hIndex(int[] citations) {
        int n = citations.length;
        int left = 0, right = n - 1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (citations[mid] >= n - mid) {
                right = mid - 1; // Tìm giá trị h lớn hơn nữa (mid nhỏ hơn)
            } else {
                left = mid + 1; // Lượt trích dẫn quá ít so với số lượng
            }
        }
        return n - left;
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(log N)
- Space Complexity: O(1)

---

## 30. Median of Two Sorted Arrays (LeetCode 4)
**Đề bài chi tiết:**
Cho hai mảng đã sắp xếp `nums1` và `nums2` có kích thước lần lượt là `m` và `n`. Trả về trung vị (median) của hai mảng được gộp lại. Thuật toán của bạn phải có độ phức tạp thời gian là $O(\log(m+n))$.

**Phân tích thuật toán:**
Do yêu cầu O(log(min(m, n))), ta thực hiện Binary Search trên mảng có kích thước nhỏ hơn (giả sử `nums1`). 
Mục tiêu là tìm một "vách ngăn" (partition) để chia cả hai mảng làm hai nửa: Nửa Trái và Nửa Phải sao cho số phần tử bên trái bằng bên phải (hoặc hơn 1 nếu tổng lẻ) và giá trị lớn nhất bên trái phải $\le$ giá trị nhỏ nhất bên phải.
Cụ thể `partitionX + partitionY = (m + n + 1) / 2`.
Tại mỗi `partitionX`, ta xác định `maxLeftX`, `minRightX`, `maxLeftY`, `minRightY`. Nếu chéo nhau hợp lệ (`maxLeftX <= minRightY` và `maxLeftY <= minRightX`), vách ngăn đã được đặt đúng. Từ đó tính ngay được trung vị dựa vào tính chẵn lẻ của tổng kích thước.

**Mã nguồn Java:**
```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        // Luôn tìm kiếm trên mảng nhỏ hơn
        if (nums1.length > nums2.length) {
            return findMedianSortedArrays(nums2, nums1);
        }
        
        int m = nums1.length;
        int n = nums2.length;
        int left = 0, right = m; // Vách ngăn chạy từ 0 đến m
        
        while (left <= right) {
            int partitionX = left + (right - left) / 2;
            int partitionY = (m + n + 1) / 2 - partitionX;
            
            int maxLeftX = (partitionX == 0) ? Integer.MIN_VALUE : nums1[partitionX - 1];
            int minRightX = (partitionX == m) ? Integer.MAX_VALUE : nums1[partitionX];
            
            int maxLeftY = (partitionY == 0) ? Integer.MIN_VALUE : nums2[partitionY - 1];
            int minRightY = (partitionY == n) ? Integer.MAX_VALUE : nums2[partitionY];
            
            if (maxLeftX <= minRightY && maxLeftY <= minRightX) {
                // Đã tìm được vách ngăn đúng
                if ((m + n) % 2 == 0) {
                    return (Math.max(maxLeftX, maxLeftY) + Math.min(minRightX, minRightY)) / 2.0;
                } else {
                    return Math.max(maxLeftX, maxLeftY);
                }
            } else if (maxLeftX > minRightY) {
                // partitionX nằm quá xa về bên phải
                right = partitionX - 1;
            } else {
                left = partitionX + 1;
            }
        }
        throw new IllegalArgumentException("Đầu vào không hợp lệ");
    }
}
```
**Độ phức tạp:**
- Time Complexity: O(log(min(m, n)))
- Space Complexity: O(1)
