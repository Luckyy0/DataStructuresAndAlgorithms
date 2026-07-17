# Companion Problems: 06-Two-Pointers-Pattern

Tài liệu này cung cấp **30 bài tập thực hành** về Two Pointers, phân loại theo 3 dạng: Opposite Direction, Same Direction, và Fast & Slow.

---

## 1. Two Sum II - Input Array Is Sorted (LeetCode 167)
**Đề bài chi tiết:** Cho mảng `numbers` ĐÃ ĐƯỢC SẮP XẾP tăng dần. Tìm 2 số có tổng bằng `target`. Trả về mảng 1-indexed `[index1, index2]`. Không dùng $\mathcal{O}(N)$ space phụ.
**Phân tích thuật toán:** Hai con trỏ ngược chiều (Opposite Direction). `left` = 0, `right` = n-1. Nếu tổng nhỏ hơn target $\rightarrow$ tăng `left`. Nếu tổng lớn hơn $\rightarrow$ giảm `right`. Dừng khi tìm thấy.
**Mã nguồn Java:**
```java
public class TwoSumII {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0, right = numbers.length - 1;
        while (left < right) {
            int sum = numbers[left] + numbers[right];
            if (sum == target) {
                return new int[]{left + 1, right + 1};
            } else if (sum < target) {
                left++;
            } else {
                right--;
            }
        }
        return new int[]{-1, -1};
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 2. 3Sum (LeetCode 15)
**Đề bài chi tiết:** Cho mảng số nguyên, tìm TẤT CẢ các bộ 3 số `[a, b, c]` sao cho $a + b + c = 0$. Các bộ 3 không được trùng lặp.
**Phân tích thuật toán:** Sắp xếp mảng trước ($\mathcal{O}(N \log N)$). Duyệt `i` từ 0 đến $N-3$, đặt target cho bài Two Sum là `-nums[i]`. `left = i + 1`, `right = n - 1`. Chú ý skip trùng lặp ở biến `i`, `left` và `right` để tránh kết quả trùng.
**Mã nguồn Java:**
```java
import java.util.*;

public class ThreeSum {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        Arrays.sort(nums);
        
        for (int i = 0; i < nums.length - 2; i++) {
            if (i > 0 && nums[i] == nums[i - 1]) continue; // Skip duplicate i
            
            int left = i + 1, right = nums.length - 1;
            int target = -nums[i];
            
            while (left < right) {
                int sum = nums[left] + nums[right];
                if (sum == target) {
                    res.add(Arrays.asList(nums[i], nums[left], nums[right]));
                    left++; right--;
                    // Skip duplicates for left & right
                    while (left < right && nums[left] == nums[left - 1]) left++;
                    while (left < right && nums[right] == nums[right + 1]) right--;
                } else if (sum < target) {
                    left++;
                } else {
                    right--;
                }
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N^2)$, Space $\mathcal{O}(1)$ (hoặc $\mathcal{O}(N)$ tùy thuộc thuật toán Sort của Java).

---

## 3. Container With Most Water (LeetCode 11)
**Đề bài chi tiết:** Cho mảng độ cao `height` của các vách ngăn. Tìm 2 vách ngăn tạo thành 1 bể chứa được nhiều nước nhất. Diện tích = $min(h[L], h[R]) \times (R - L)$.
**Phân tích thuật toán:** Bắt đầu với $L=0, R=N-1$ (chiều rộng tối đa). Cập nhật `maxArea`. Sau đó, ta muốn tăng chiều cao để hy vọng diện tích lớn hơn. Vì vậy, ta LUÔN LUÔN di chuyển con trỏ nào có độ cao THẤP HƠN vào trong.
**Mã nguồn Java:**
```java
public class ContainerMostWater {
    public int maxArea(int[] height) {
        int left = 0, right = height.length - 1;
        int maxArea = 0;
        
        while (left < right) {
            int currentArea = Math.min(height[left], height[right]) * (right - left);
            maxArea = Math.max(maxArea, currentArea);
            
            if (height[left] < height[right]) {
                left++; // Bỏ cái thấp đi, mong tìm cái cao hơn
            } else {
                right--;
            }
        }
        return maxArea;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 4. Remove Duplicates from Sorted Array II (LeetCode 80)
**Đề bài chi tiết:** Cho mảng đã sắp xếp. Xóa in-place các phần tử trùng lặp sao cho mỗi phần tử xuất hiện tối đa 2 lần. Trả về độ dài mới.
**Phân tích thuật toán:** Hai con trỏ cùng chiều (Same Direction). `writer` bắt đầu từ chỉ số 2. `reader` chạy từ 2. Nếu `nums[reader] != nums[writer - 2]`, ta copy đè `nums[writer] = nums[reader]` và `writer++`.
**Mã nguồn Java:**
```java
public class RemoveDuplicatesII {
    public int removeDuplicates(int[] nums) {
        if (nums.length <= 2) return nums.length;
        
        int writer = 2;
        for (int reader = 2; reader < nums.length; reader++) {
            // Chỉ ghi nếu giá trị hiện tại khác giá trị ở (vị trí ghi - 2)
            if (nums[reader] != nums[writer - 2]) {
                nums[writer] = nums[reader];
                writer++;
            }
        }
        return writer;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 5. Sort Colors / Dutch National Flag (LeetCode 75)
**Đề bài chi tiết:** Cho mảng chứa 0 (Đỏ), 1 (Trắng), 2 (Xanh). Sắp xếp mảng tăng dần. (In-place, 1 vòng lặp duy nhất).
**Phân tích thuật toán:** Dùng 3 Pointers: `low` (ranh giới 0), `mid` (con trỏ đang quét), `high` (ranh giới 2).
- Nếu `mid == 0`, swap `mid` và `low`, tăng cả hai.
- Nếu `mid == 1`, tăng `mid`.
- Nếu `mid == 2`, swap `mid` và `high`, CHỈ giảm `high` (vì số đổi từ `high` về có thể là 0, 1 hoặc 2, cần xét lại ở vòng sau).
**Mã nguồn Java:**
```java
public class SortColors {
    public void sortColors(int[] nums) {
        int low = 0, mid = 0, high = nums.length - 1;
        
        while (mid <= high) {
            if (nums[mid] == 0) {
                swap(nums, low++, mid++);
            } else if (nums[mid] == 1) {
                mid++;
            } else {
                swap(nums, mid, high--);
            }
        }
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 6. Valid Palindrome (LeetCode 125)
**Đề bài chi tiết:** Kiểm tra xem chuỗi có phải là Palindrome không (Chỉ xét chữ và số, bỏ qua khoảng trắng/dấu, không phân biệt hoa thường).
**Phân tích thuật toán:** Hai con trỏ `left` và `right`. `Character.isLetterOrDigit` để lướt qua ký tự không hợp lệ. `Character.toLowerCase` để so sánh.
**Mã nguồn Java:**
```java
public class ValidPalindrome {
    public boolean isPalindrome(String s) {
        int left = 0, right = s.length() - 1;
        
        while (left < right) {
            while (left < right && !Character.isLetterOrDigit(s.charAt(left))) left++;
            while (left < right && !Character.isLetterOrDigit(s.charAt(right))) right--;
            
            if (left < right) {
                if (Character.toLowerCase(s.charAt(left)) != Character.toLowerCase(s.charAt(right))) {
                    return false;
                }
                left++; right--;
            }
        }
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 7. Squares of a Sorted Array (LeetCode 977)
**Đề bài chi tiết:** Cho mảng đã sắp xếp (có số âm). Bình phương tất cả các phần tử và trả về mảng kết quả cũng được sắp xếp tăng dần. Cấm dùng hàm Sort.
**Phân tích thuật toán:** Vì có số âm (bình phương lên có thể rất lớn), giá trị lớn nhất luôn nằm ở **Cực trái** hoặc **Cực phải**. Dùng 2 con trỏ `left = 0`, `right = n-1`. So sánh bình phương của 2 cực, giá trị nào lớn hơn thì ghi vào CUỐI mảng kết quả, thu hẹp con trỏ đó.
**Mã nguồn Java:**
```java
public class SortedSquares {
    public int[] sortedSquares(int[] nums) {
        int n = nums.length;
        int[] res = new int[n];
        int left = 0, right = n - 1;
        int writer = n - 1; // Điền mảng kết quả từ cuối lên
        
        while (left <= right) {
            int sqLeft = nums[left] * nums[left];
            int sqRight = nums[right] * nums[right];
            
            if (sqLeft > sqRight) {
                res[writer--] = sqLeft;
                left++;
            } else {
                res[writer--] = sqRight;
                right--;
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$ cho mảng kết quả.

---

## 8. Merge Sorted Array In-Place (LeetCode 88)
**Đề bài chi tiết:** Cho mảng `nums1` độ dài $M+N$ (có sẵn các rác 0 ở $N$ phần tử cuối) và `nums2` độ dài $N$. Gộp `nums2` vào `nums1` thành 1 mảng đã sắp xếp. $\mathcal{O}(1)$ không gian.
**Phân tích thuật toán:** Nếu điền từ đầu, ta sẽ đè mất dữ liệu của `nums1`. Giải pháp: Three Pointers, **Điền từ cuối lên**. `p1 = m-1`, `p2 = n-1`, `writer = m+n-1`. Lấy số lớn hơn ghi vào sau.
**Mã nguồn Java:**
```java
public class MergeSortedArray {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int p1 = m - 1;
        int p2 = n - 1;
        int writer = m + n - 1;
        
        while (p2 >= 0) { // Khi nums2 hết thì tự nums1 đã sorted đúng
            if (p1 >= 0 && nums1[p1] > nums2[p2]) {
                nums1[writer--] = nums1[p1--];
            } else {
                nums1[writer--] = nums2[p2--];
            }
        }
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M+N)$, Space $\mathcal{O}(1)$.

---

## 9. Trapping Rain Water - Two Pointers Approach (LeetCode 42)
**Đề bài chi tiết:** Cho mảng độ cao địa hình, tính tổng lượng nước mưa đọng lại sau khi mưa. Yêu cầu Space $\mathcal{O}(1)$.
**Phân tích thuật toán:** Mực nước tại ô $i$ bị giới hạn bởi $\min(\text{maxLeft}, \text{maxRight})$.
Dùng 2 con trỏ `left` và `right`. Duy trì biến `maxLeft` và `maxRight`. Bên nào NHỎ HƠN thì bên đó quyết định mực nước (Nước sẽ tràn ra từ bên thấp). Nên ta sẽ tính toán cho bên thấp trước và di chuyển con trỏ.
**Mã nguồn Java:**
```java
public class TrappingRainWater {
    public int trap(int[] height) {
        int left = 0, right = height.length - 1;
        int maxLeft = 0, maxRight = 0;
        int totalWater = 0;
        
        while (left < right) {
            if (height[left] < height[right]) {
                if (height[left] >= maxLeft) maxLeft = height[left];
                else totalWater += maxLeft - height[left];
                left++;
            } else {
                if (height[right] >= maxRight) maxRight = height[right];
                else totalWater += maxRight - height[right];
                right--;
            }
        }
        return totalWater;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 10. Linked List Cycle Detection (LeetCode 141)
**Đề bài chi tiết:** Phát hiện xem Danh sách liên kết có chứa chu trình hay không. Không dùng HashMap.
**Phân tích thuật toán:** Fast & Slow pointers (Rùa và Thỏ). Slow đi 1 bước, Fast đi 2 bước. Nếu có chu trình, 2 con trỏ chắc chắn sẽ gặp nhau ở một vòng lặp nào đó. Nếu Fast chạm Null $\rightarrow$ Không có chu trình.
**Mã nguồn Java:**
```java
// Giả định Class ListNode đã tồn tại
public class CycleDetection {
    class ListNode { int val; ListNode next; }
    
    public boolean hasCycle(ListNode head) {
        if (head == null) return false;
        
        ListNode slow = head;
        ListNode fast = head;
        
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            
            if (slow == fast) {
                return true;
            }
        }
        return false;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 11. 3Sum Closest (LeetCode 16)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` và số nguyên `target`. Tìm 3 số trong `nums` sao cho tổng của chúng gần nhất với `target`. Trả về tổng của 3 số đó.
**Phân tích thuật toán:** Tương tự 3Sum, ta sắp xếp mảng trước. Duyệt `i` từ 0 đến $N-3$, dùng hai con trỏ `left = i + 1`, `right = N - 1`. Tính tổng `sum`. Nếu `|target - sum| < |target - closestSum|` thì cập nhật `closestSum`. Nếu `sum < target` thì tăng `left`, ngược lại giảm `right`.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class ThreeSumClosest {
    public int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        int closestSum = nums[0] + nums[1] + nums[2];
        
        for (int i = 0; i < nums.length - 2; i++) {
            int left = i + 1, right = nums.length - 1;
            while (left < right) {
                int sum = nums[i] + nums[left] + nums[right];
                if (Math.abs(target - sum) < Math.abs(target - closestSum)) {
                    closestSum = sum;
                }
                
                if (sum < target) {
                    left++;
                } else if (sum > target) {
                    right--;
                } else {
                    return sum; // Nếu bằng target thì khoảng cách là 0 (nhỏ nhất có thể)
                }
            }
        }
        return closestSum;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N^2)$, Space $\mathcal{O}(1)$.

---

## 12. 4Sum (LeetCode 18)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` và số `target`. Tìm tất cả các bộ 4 số `[a, b, c, d]` sao cho $a + b + c + d = target$. Các bộ 4 không được trùng lặp.
**Phân tích thuật toán:** Sắp xếp mảng. Thêm một vòng lặp `j` lồng bên trong vòng lặp `i` của 3Sum. Cần loại bỏ các phần tử trùng lặp cho cả `i` và `j`. Do tổng 4 số nguyên có thể vượt quá giới hạn 32-bit của `int`, ta cần ép kiểu sang `long` khi tính tổng.
**Mã nguồn Java:**
```java
import java.util.*;

public class FourSum {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums == null || nums.length < 4) return res;
        Arrays.sort(nums);
        
        for (int i = 0; i < nums.length - 3; i++) {
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            
            for (int j = i + 1; j < nums.length - 2; j++) {
                if (j > i + 1 && nums[j] == nums[j - 1]) continue;
                
                int left = j + 1;
                int right = nums.length - 1;
                while (left < right) {
                    long sum = (long) nums[i] + nums[j] + nums[left] + nums[right];
                    if (sum == target) {
                        res.add(Arrays.asList(nums[i], nums[j], nums[left], nums[right]));
                        left++;
                        right--;
                        while (left < right && nums[left] == nums[left - 1]) left++;
                        while (left < right && nums[right] == nums[right + 1]) right--;
                    } else if (sum < target) {
                        left++;
                    } else {
                        right--;
                    }
                }
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N^3)$, Space $\mathcal{O}(1)$ hoặc $\mathcal{O}(N)$ (phụ thuộc vào thuật toán Sort).

---

## 13. Boats to Save People (LeetCode 881)
**Đề bài chi tiết:** Cho mảng `people` biểu diễn cân nặng của mọi người và số `limit` là tải trọng tối đa của một chiếc thuyền. Mỗi thuyền chở được tối đa 2 người và tổng cân nặng không vượt quá `limit`. Tìm số thuyền tối thiểu cần dùng.
**Phân tích thuật toán:** Sắp xếp mảng cân nặng tăng dần. Dùng hai con trỏ `left` (người nhẹ nhất) và `right` (người nặng nhất). Nếu `people[left] + people[right] <= limit`, 2 người này có thể đi chung thuyền $\rightarrow$ `left++`, `right--`. Ngược lại, người nặng nhất phải đi 1 mình $\rightarrow$ chỉ giảm `right`. Cứ thế đếm số thuyền.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class BoatsToSavePeople {
    public int numRescueBoats(int[] people, int limit) {
        Arrays.sort(people);
        int left = 0, right = people.length - 1;
        int boats = 0;
        
        while (left <= right) {
            // Dù đi chung hay đi riêng, thì người nặng nhất vẫn phải lên thuyền
            if (people[left] + people[right] <= limit) {
                left++;
            }
            right--;
            boats++;
        }
        
        return boats;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log N)$ (do sắp xếp), Space $\mathcal{O}(1)$ (hoặc $\mathcal{O}(N)$ cho thuật toán sắp xếp).

---

## 14. Valid Palindrome II (LeetCode 680)
**Đề bài chi tiết:** Cho một chuỗi `s`. Trả về `true` nếu nó có thể trở thành chuỗi Palindrome sau khi xóa TỐI ĐA một ký tự, ngược lại trả về `false`.
**Phân tích thuật toán:** Dùng hai con trỏ `left` và `right` kiểm tra tính đối xứng. Khi gặp kí tự khác nhau `s.charAt(left) != s.charAt(right)`, ta có 2 lựa chọn: xóa `left` hoặc xóa `right`. Ta gọi hàm kiểm tra xem đoạn con `(left + 1, right)` hoặc `(left, right - 1)` có phải là Palindrome không. Nếu 1 trong 2 là Palindrome, trả về `true`.
**Mã nguồn Java:**
```java
public class ValidPalindromeII {
    public boolean validPalindrome(String s) {
        int left = 0, right = s.length() - 1;
        
        while (left < right) {
            if (s.charAt(left) != s.charAt(right)) {
                return isPalindrome(s, left + 1, right) || isPalindrome(s, left, right - 1);
            }
            left++;
            right--;
        }
        return true;
    }
    
    private boolean isPalindrome(String s, int left, int right) {
        while (left < right) {
            if (s.charAt(left) != s.charAt(right)) {
                return false;
            }
            left++;
            right--;
        }
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 15. Backspace String Compare (LeetCode 844)
**Đề bài chi tiết:** Cho hai chuỗi `s` và `t` chứa ký tự '#' đại diện cho nút Backspace (xóa ký tự liền trước). Kiểm tra xem hai chuỗi sau khi gõ xong có bằng nhau không. $\mathcal{O}(N)$ Time và $\mathcal{O}(1)$ Space.
**Phân tích thuật toán:** Để đạt $\mathcal{O}(1)$ Space, duyệt chuỗi từ cuối lên đầu bằng 2 con trỏ. Dùng 2 biến `skipS` và `skipT` để theo dõi số lượng dấu '#' cần áp dụng. Bỏ qua các ký tự bị xóa và so sánh từng ký tự hợp lệ còn lại của `s` và `t`.
**Mã nguồn Java:**
```java
public class BackspaceStringCompare {
    public boolean backspaceCompare(String s, String t) {
        int i = s.length() - 1, j = t.length() - 1;
        int skipS = 0, skipT = 0;
        
        while (i >= 0 || j >= 0) {
            // Tìm ký tự hợp lệ tiếp theo của s
            while (i >= 0) {
                if (s.charAt(i) == '#') { skipS++; i--; }
                else if (skipS > 0) { skipS--; i--; }
                else break;
            }
            // Tìm ký tự hợp lệ tiếp theo của t
            while (j >= 0) {
                if (t.charAt(j) == '#') { skipT++; j--; }
                else if (skipT > 0) { skipT--; j--; }
                else break;
            }
            
            // So sánh 2 ký tự hợp lệ
            if (i >= 0 && j >= 0 && s.charAt(i) != t.charAt(j)) return false;
            // Nếu 1 chuỗi đã duyệt hết nhưng chuỗi kia vẫn còn ký tự hợp lệ
            if ((i >= 0) != (j >= 0)) return false;
            
            i--; j--;
        }
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N + M)$, Space $\mathcal{O}(1)$.

---

## 16. Intersection of Two Arrays II (LeetCode 350)
**Đề bài chi tiết:** Cho hai mảng số nguyên `nums1` và `nums2`. Tìm mảng giao (phần tử chung) của hai mảng. Mỗi phần tử trong mảng kết quả phải xuất hiện với số lần bằng với số lần nó xuất hiện chung trong cả hai mảng.
**Phân tích thuật toán:** Trước tiên sắp xếp cả 2 mảng. Dùng 2 con trỏ `i` và `j` duyệt qua `nums1` và `nums2`. Nếu `nums1[i] < nums2[j]`, tăng `i`. Nếu `nums1[i] > nums2[j]`, tăng `j`. Nếu bằng nhau, thêm vào kết quả rồi tăng cả hai. (Giống tư tưởng trộn mảng Merge Sort).
**Mã nguồn Java:**
```java
import java.util.*;

public class IntersectionOfTwoArraysII {
    public int[] intersect(int[] nums1, int[] nums2) {
        Arrays.sort(nums1);
        Arrays.sort(nums2);
        
        int i = 0, j = 0;
        List<Integer> list = new ArrayList<>();
        
        while (i < nums1.length && j < nums2.length) {
            if (nums1[i] < nums2[j]) {
                i++;
            } else if (nums1[i] > nums2[j]) {
                j++;
            } else {
                list.add(nums1[i]);
                i++;
                j++;
            }
        }
        
        int[] res = new int[list.size()];
        for (int k = 0; k < list.size(); k++) {
            res[k] = list.get(k);
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log N + M \log M)$ (do sắp xếp), Space $\mathcal{O}(\min(N, M))$ (để chứa kết quả).

---

## 17. Next Permutation (LeetCode 31)
**Đề bài chi tiết:** Tìm hoán vị kế tiếp (theo thứ tự từ điển) của một mảng các số nguyên. In-place và chỉ sử dụng $\mathcal{O}(1)$ bộ nhớ phụ.
**Phân tích thuật toán:**
1. Duyệt từ phải sang trái tìm vị trí đầu tiên `i` sao cho `nums[i] < nums[i+1]` (điểm gãy).
2. Nếu tìm thấy `i`, duyệt từ phải sang trái tìm vị trí `j` đầu tiên sao cho `nums[j] > nums[i]`. Hoán đổi `nums[i]` và `nums[j]`.
3. Lật ngược mảng từ `i + 1` đến cuối mảng (sắp xếp tăng dần đoạn này để có thứ tự từ điển nhỏ nhất trong các hoán vị lớn hơn).
**Mã nguồn Java:**
```java
public class NextPermutation {
    public void nextPermutation(int[] nums) {
        int i = nums.length - 2;
        // 1. Tìm phần tử đầu tiên phá vỡ thứ tự giảm dần từ cuối
        while (i >= 0 && nums[i] >= nums[i + 1]) {
            i--;
        }
        
        if (i >= 0) {
            int j = nums.length - 1;
            // 2. Tìm phần tử nhỏ nhất bên phải mà vẫn lớn hơn nums[i]
            while (j >= 0 && nums[j] <= nums[i]) {
                j--;
            }
            swap(nums, i, j);
        }
        
        // 3. Lật ngược (Reverse) đoạn từ i+1 đến cuối để được thứ tự từ điển nhỏ nhất
        reverse(nums, i + 1, nums.length - 1);
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
    }
    
    private void reverse(int[] nums, int start, int end) {
        while (start < end) {
            swap(nums, start, end);
            start++;
            end--;
        }
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 18. Partition Labels (LeetCode 763)
**Đề bài chi tiết:** Cho một chuỗi `s`. Chia chuỗi thành nhiều phần (partition) càng tốt sao cho mỗi ký tự chỉ xuất hiện trong tối đa một phần. Trả về mảng kích thước của các phần đó.
**Phân tích thuật toán:** Đầu tiên duyệt chuỗi để lưu vị trí xuất hiện cuối cùng của mỗi ký tự vào mảng `last[]`. Duyệt lại chuỗi, dùng hai con trỏ `start` và `end`. Biến `end` liên tục cập nhật vị trí xuất hiện cuối cùng lớn nhất của các ký tự đang xét (`end = max(end, last[s.charAt(i) - 'a'])`). Khi `i == end`, ta đã tìm được một cụm hợp lệ $\rightarrow$ lưu độ dài `end - start + 1` và cập nhật `start = i + 1`.
**Mã nguồn Java:**
```java
import java.util.*;

public class PartitionLabels {
    public List<Integer> partitionLabels(String s) {
        int[] last = new int[26];
        // Lưu vị trí xuất hiện cuối cùng của từng ký tự
        for (int i = 0; i < s.length(); i++) {
            last[s.charAt(i) - 'a'] = i;
        }
        
        List<Integer> res = new ArrayList<>();
        int start = 0, end = 0;
        
        for (int i = 0; i < s.length(); i++) {
            end = Math.max(end, last[s.charAt(i) - 'a']);
            // Khi duyệt đến vị trí end hiện tại, ta có thể tách 1 phần
            if (i == end) {
                res.add(end - start + 1);
                start = i + 1;
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$ (mảng `last` có 26 phần tử).

---

## 19. Is Subsequence (LeetCode 392)
**Đề bài chi tiết:** Cho chuỗi `s` và `t`, kiểm tra xem `s` có phải là chuỗi con (subsequence) của `t` hay không. Chuỗi con có thể tạo ra bằng cách xóa một số ký tự của chuỗi gốc mà không thay đổi thứ tự các ký tự còn lại.
**Phân tích thuật toán:** Dùng hai con trỏ `i` và `j`. Con trỏ `i` duyệt chuỗi `s`, `j` duyệt chuỗi `t`. Nếu `s.charAt(i) == t.charAt(j)` thì ta tìm được một ký tự khớp, tăng `i`. Bất kể khớp hay không, luôn tăng `j`. Nếu `i` đi hết chuỗi `s` thì tức là `s` là chuỗi con của `t`.
**Mã nguồn Java:**
```java
public class IsSubsequence {
    public boolean isSubsequence(String s, String t) {
        int i = 0, j = 0;
        while (i < s.length() && j < t.length()) {
            if (s.charAt(i) == t.charAt(j)) {
                i++;
            }
            j++;
        }
        // Nếu i duyệt hết chuỗi s tức là mọi ký tự của s đều xuất hiện theo đúng thứ tự trong t
        return i == s.length();
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M)$ với $M$ là chiều dài chuỗi `t`, Space $\mathcal{O}(1)$.

---

## 20. Reverse Vowels of a String (LeetCode 345)
**Đề bài chi tiết:** Cho chuỗi `s`, lật ngược (reverse) chỉ riêng các ký tự nguyên âm trong chuỗi và trả về kết quả. Các nguyên âm gồm 'a', 'e', 'i', 'o', 'u' (cả viết hoa và viết thường).
**Phân tích thuật toán:** Dùng 2 con trỏ `left` và `right` ở 2 đầu chuỗi. Nếu cả `left` và `right` đều chỉ vào nguyên âm, hoán đổi chúng và di chuyển cả hai con trỏ. Nếu không, di chuyển con trỏ nào chưa chỉ vào nguyên âm. Chuyển String thành mảng ký tự (`char[]`) để dễ hoán đổi.
**Mã nguồn Java:**
```java
public class ReverseVowels {
    public String reverseVowels(String s) {
        char[] arr = s.toCharArray();
        int left = 0, right = arr.length - 1;
        String vowels = "aeiouAEIOU";
        
        while (left < right) {
            // Lướt qua các ký tự không phải nguyên âm
            while (left < right && vowels.indexOf(arr[left]) == -1) left++;
            while (left < right && vowels.indexOf(arr[right]) == -1) right--;
            
            // Nếu cả 2 đều là nguyên âm thì hoán đổi
            if (left < right) {
                char temp = arr[left];
                arr[left] = arr[right];
                arr[right] = temp;
                left++;
                right--;
            }
        }
        return new String(arr);
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$ (vì phải đổi chuỗi String không thay đổi (immutable) thành mảng `char[]` trong Java).

---

## 21. Reverse Words in a String (LeetCode 151)
**Đề bài chi tiết:** Cho một chuỗi `s` chứa các từ cách nhau bởi khoảng trắng. Lật ngược thứ tự các từ trong chuỗi. Kết quả phải là một chuỗi các từ cách nhau bởi đúng 1 khoảng trắng, không có khoảng trắng thừa ở hai đầu.
**Phân tích thuật toán:** Mặc dù Java cung cấp `s.split("\\s+")`, cách tối ưu là sử dụng Two Pointers (hoặc `char[]`) để làm in-place (trong C++) hoặc tiết kiệm chi phí tạo nhiều chuỗi con. Có thể dùng `StringBuilder`. Đầu tiên, bỏ khoảng trắng thừa. Lật ngược (reverse) toàn bộ chuỗi. Cuối cùng lật ngược từng từ riêng biệt. Trong Java, dùng `StringBuilder` là hiệu quả nhất.
**Mã nguồn Java:**
```java
public class ReverseWords {
    public String reverseWords(String s) {
        StringBuilder sb = trimSpaces(s);
        reverse(sb, 0, sb.length() - 1);
        reverseEachWord(sb);
        return sb.toString();
    }
    
    private StringBuilder trimSpaces(String s) {
        int left = 0, right = s.length() - 1;
        while (left <= right && s.charAt(left) == ' ') left++;
        while (left <= right && s.charAt(right) == ' ') right--;
        
        StringBuilder sb = new StringBuilder();
        while (left <= right) {
            char c = s.charAt(left);
            if (c != ' ') {
                sb.append(c);
            } else if (sb.charAt(sb.length() - 1) != ' ') {
                sb.append(c);
            }
            left++;
        }
        return sb;
    }
    
    private void reverse(StringBuilder sb, int left, int right) {
        while (left < right) {
            char temp = sb.charAt(left);
            sb.setCharAt(left++, sb.charAt(right));
            sb.setCharAt(right--, temp);
        }
    }
    
    private void reverseEachWord(StringBuilder sb) {
        int n = sb.length();
        int start = 0, end = 0;
        
        while (start < n) {
            while (end < n && sb.charAt(end) != ' ') end++;
            reverse(sb, start, end - 1);
            start = end + 1;
            end = start;
        }
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$ do String trong Java không thể thay đổi in-place.

---

## 22. String Compression (LeetCode 443)
**Đề bài chi tiết:** Cho một mảng ký tự `chars`. Nén nó in-place: Nếu nhóm ký tự liên tiếp có độ dài 1 thì giữ nguyên, nếu $>1$ thì viết ký tự theo sau là độ dài nhóm đó. Trả về độ dài mảng sau khi nén. Mảng `chars` phải được sửa đổi để chứa chuỗi nén này.
**Phân tích thuật toán:** Dùng 3 con trỏ: `read` (duyệt mảng), `write` (vị trí ghi vào mảng), và `start` (đánh dấu bắt đầu của một nhóm ký tự giống nhau). Khi `chars[read] != chars[read+1]`, ta ghi `chars[start]` vào `write`, sau đó nếu độ dài nhóm > 1, biến độ dài thành chuỗi rồi ghi từng chữ số vào mảng.
**Mã nguồn Java:**
```java
public class StringCompression {
    public int compress(char[] chars) {
        int write = 0, read = 0;
        while (read < chars.length) {
            int start = read;
            // Di chuyển read đến cuối nhóm kí tự giống nhau
            while (read < chars.length && chars[read] == chars[start]) {
                read++;
            }
            
            // Ghi ký tự vào vị trí write
            chars[write++] = chars[start];
            
            // Tính số lượng và ghi ra nếu > 1
            int count = read - start;
            if (count > 1) {
                String countStr = String.valueOf(count);
                for (char c : countStr.toCharArray()) {
                    chars[write++] = c;
                }
            }
        }
        return write;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 23. Middle of the Linked List (LeetCode 876)
**Đề bài chi tiết:** Cho một danh sách liên kết đơn (Singly Linked List). Tìm phần tử ở giữa. Nếu có 2 phần tử giữa (số chẵn) thì trả về phần tử thứ hai.
**Phân tích thuật toán:** Dùng thuật toán Rùa và Thỏ (Fast & Slow Pointers). Con trỏ Rùa (Slow) bước 1 bước mỗi lần, con trỏ Thỏ (Fast) bước 2 bước mỗi lần. Khi con trỏ Thỏ đi đến cuối danh sách (chạm null hoặc node cuối), thì con trỏ Rùa sẽ nằm ở vị trí chính giữa.
**Mã nguồn Java:**
```java
// Giả định Class ListNode đã tồn tại
public class MiddleOfLinkedList {
    class ListNode { int val; ListNode next; }

    public ListNode middleNode(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;
        
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        return slow;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 24. Remove Nth Node From End of List (LeetCode 19)
**Đề bài chi tiết:** Xóa phần tử thứ $n$ đếm từ cuối của Danh sách liên kết và trả về `head`.
**Phân tích thuật toán:** Dùng 2 con trỏ `first` và `second`. Cho `first` chạy trước $n$ bước. Sau đó cả hai con trỏ cùng chạy cho đến khi `first` đi đến node cuối cùng (tức `first.next == null`). Lúc này, con trỏ `second` sẽ dừng ở vị trí TRƯỚC node cần xóa. Để thuận tiện khi phải xóa node ở đầu (head), sử dụng 1 node giả `dummy` chỉ vào `head`.
**Mã nguồn Java:**
```java
public class RemoveNthNode {
    class ListNode { int val; ListNode next; ListNode(int v) { val = v; } }

    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode first = dummy;
        ListNode second = dummy;
        
        // Cho first chạy trước n + 1 bước (so với second để lấy vị trí TRƯỚC phần tử cần xóa)
        for (int i = 0; i <= n; i++) {
            first = first.next;
        }
        
        while (first != null) {
            first = first.next;
            second = second.next;
        }
        
        // Bỏ qua phần tử cần xóa
        second.next = second.next.next;
        
        return dummy.next;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 25. Palindrome Linked List (LeetCode 234)
**Đề bài chi tiết:** Kiểm tra xem một danh sách liên kết đơn có phải là chuỗi đối xứng (Palindrome) hay không. Yêu cầu Time $\mathcal{O}(N)$ và Space $\mathcal{O}(1)$.
**Phân tích thuật toán:**
1. Dùng Slow / Fast pointer để tìm phần tử giữa danh sách (giống Bài 23).
2. Lật ngược (Reverse) nửa sau của danh sách liên kết từ vị trí giữa trở đi.
3. Dùng 2 con trỏ so sánh từng phần tử từ `head` (đầu tiên) và `head` mới của nửa sau.
**Mã nguồn Java:**
```java
public class PalindromeLinkedList {
    class ListNode { int val; ListNode next; }

    public boolean isPalindrome(ListNode head) {
        if (head == null || head.next == null) return true;
        
        // 1. Tìm điểm giữa
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        // 2. Lật ngược nửa sau
        ListNode secondHalf = reverseList(slow);
        ListNode firstHalf = head;
        ListNode temp = secondHalf; // Lưu lại để sau này phục hồi (nếu muốn, không bắt buộc)
        
        // 3. So sánh
        boolean result = true;
        while (result && secondHalf != null) {
            if (firstHalf.val != secondHalf.val) {
                result = false;
            }
            firstHalf = firstHalf.next;
            secondHalf = secondHalf.next;
        }
        
        return result;
    }
    
    private ListNode reverseList(ListNode head) {
        ListNode prev = null;
        ListNode current = head;
        while (current != null) {
            ListNode nextTemp = current.next;
            current.next = prev;
            prev = current;
            current = nextTemp;
        }
        return prev;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 26. Assign Cookies (LeetCode 455)
**Đề bài chi tiết:** Bạn có một mảng `g` là mức độ thỏa mãn tối thiểu của từng đứa trẻ và mảng `s` là kích thước của từng chiếc bánh quy. Mỗi đứa trẻ tối đa nhận 1 bánh quy. Nếu $s[j] \ge g[i]$, trẻ $i$ sẽ thỏa mãn. Tìm số lượng trẻ nhiều nhất có thể làm thỏa mãn.
**Phân tích thuật toán:** Sắp xếp cả hai mảng. Dùng con trỏ `i` cho mảng trẻ em và `j` cho mảng bánh quy. Tham lam: Đứa trẻ dễ dãi nhất (mức độ nhỏ nhất) sẽ ăn chiếc bánh nhỏ nhất có thể làm nó thỏa mãn. Nếu bánh quy `j` thỏa mãn trẻ `i`, tăng cả `i` và `j`. Nếu không, chỉ tăng `j` để kiếm cái bánh to hơn.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class AssignCookies {
    public int findContentChildren(int[] g, int[] s) {
        Arrays.sort(g);
        Arrays.sort(s);
        
        int i = 0, j = 0;
        int count = 0;
        
        while (i < g.length && j < s.length) {
            if (s[j] >= g[i]) {
                // Bánh thỏa mãn
                count++;
                i++;
                j++;
            } else {
                // Bánh quá nhỏ, kiếm bánh to hơn
                j++;
            }
        }
        
        return count;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log N + M \log M)$ (sắp xếp), Space $\mathcal{O}(1)$.

---

## 27. Push Dominoes (LeetCode 838)
**Đề bài chi tiết:** Cho chuỗi các quân domino, trong đó 'L' là đẩy sang trái, 'R' là đẩy sang phải, '.' là chưa có tác động. Khi đẩy thì quân sẽ đổ chồng lên nhau. Tìm trạng thái cuối cùng của toàn bộ dãy domino.
**Phân tích thuật toán:** Dùng Two Pointers để tìm 2 lực đẩy gần nhất kề bên một đoạn dấu chấm `'.'`. Thêm domino `'L'` ở đầu bên trái và `'R'` ở đầu bên phải để dễ tính. Giữa con trỏ `i` và `j`:
- Nếu `chars[i] == chars[j]`, tất cả `'.'` ở giữa biến thành ký tự đó.
- Nếu `chars[i] == 'L'` và `chars[j] == 'R'`, không tác động vào giữa.
- Nếu `chars[i] == 'R'` và `chars[j] == 'L'`, chúng sẽ đâm vào giữa (cần tính khoảng cách để chia đôi đoạn đổ sang trái, đoạn đổ sang phải).
**Mã nguồn Java:**
```java
public class PushDominoes {
    public String pushDominoes(String dominoes) {
        // Thêm các quân giả định ở 2 đầu
        String d = "L" + dominoes + "R";
        char[] chars = d.toCharArray();
        int i = 0; // Con trỏ trái
        
        for (int j = 1; j < chars.length; j++) {
            if (chars[j] == '.') continue;
            
            int length = j - i - 1;
            
            if (chars[i] == chars[j]) {
                for (int k = 1; k <= length; k++) chars[i + k] = chars[i];
            } else if (chars[i] == 'R' && chars[j] == 'L') {
                int left = i + 1, right = j - 1;
                while (left < right) {
                    chars[left++] = 'R';
                    chars[right--] = 'L';
                }
            } // Trường hợp L...R thì không làm gì cả
            
            i = j; // Cập nhật con trỏ trái
        }
        
        StringBuilder sb = new StringBuilder();
        // Bỏ quân giả định ở 2 đầu
        for (int k = 1; k < chars.length - 1; k++) {
            sb.append(chars[k]);
        }
        return sb.toString();
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$ (lưu chuỗi kết quả).

---

## 28. Find K-th Smallest Pair Distance (LeetCode 719)
**Đề bài chi tiết:** Cho mảng `nums` nguyên. Trả về khoảng cách lớn thứ $K$ của tất cả các cặp có thể. (Khoảng cách = trị tuyệt đối hiệu 2 số).
**Phân tích thuật toán:** Sort mảng. Do khoảng cách có thể từ 0 đến $nums[n-1] - nums[0]$, sử dụng Binary Search trên khoảng giá trị (từ 0 đến Max_Diff). Ở mỗi bước Binary Search với khoảng cách dự đoán là `mid`, ta dùng Two Pointers (Sliding Window) để đếm số cặp có khoảng cách $\le mid$. Nếu số cặp $\ge K$, ta thu hẹp `right = mid`; ngược lại `left = mid + 1`.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class KthSmallestPairDistance {
    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums);
        int n = nums.length;
        
        int low = 0;
        int high = nums[n - 1] - nums[0];
        
        while (low < high) {
            int mid = low + (high - low) / 2;
            
            // Đếm số lượng cặp có khoảng cách <= mid (dùng Two Pointers)
            int count = 0;
            int left = 0;
            for (int right = 0; right < n; right++) {
                while (nums[right] - nums[left] > mid) {
                    left++;
                }
                count += right - left;
            }
            
            if (count >= k) {
                high = mid; // Có thể mid là kết quả, tìm nhỏ hơn nữa
            } else {
                low = mid + 1;
            }
        }
        return low;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log N + N \log D)$ (trong đó $D$ là Max\_Diff), Space $\mathcal{O}(1)$.

---

## 29. Minimum Length of String After Deleting Similar Ends (LeetCode 1750)
**Đề bài chi tiết:** Cắt bỏ (Trim) nếu kí tự 2 đầu giống nhau. Cần chú ý khi các con trỏ tụm lại thành 1 kí tự. Lặp lại quá trình xóa tiền tố và hậu tố nếu chúng giống nhau. Trả về độ dài chuỗi còn lại.
**Phân tích thuật toán:** Dùng Two Pointers `left = 0` và `right = n - 1`. Trong khi `left < right` và `s.charAt(left) == s.charAt(right)`, lặp qua chuỗi, bỏ qua tất cả các ký tự giống nhau liên tiếp ở bên trái (`left++`) và bên phải (`right--`). Nếu `left == right`, chuỗi còn lại độ dài 1. Cuối cùng, trả về `right - left + 1` (nếu `left > right` thì độ dài là 0).
**Mã nguồn Java:**
```java
public class DeleteSimilarEnds {
    public int minimumLength(String s) {
        int left = 0, right = s.length() - 1;
        
        while (left < right && s.charAt(left) == s.charAt(right)) {
            char c = s.charAt(left);
            
            // Bỏ qua các ký tự giống c ở bên trái
            while (left <= right && s.charAt(left) == c) {
                left++;
            }
            
            // Bỏ qua các ký tự giống c ở bên phải
            while (left <= right && s.charAt(right) == c) {
                right--;
            }
        }
        
        return Math.max(0, right - left + 1);
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 30. Longest Word in Dictionary through Deleting (LeetCode 524)
**Đề bài chi tiết:** Cho chuỗi `s` và một danh sách các từ trong từ điển (dictionary). Tìm từ dài nhất trong từ điển mà có thể tạo thành bằng cách xóa một số ký tự của `s` (tức là từ đó là subsequence của `s`). Nếu có nhiều từ cùng độ dài, trả về từ có thứ tự từ điển nhỏ nhất.
**Phân tích thuật toán:** Dùng Two Pointers kiểm tra Subsequence (giống Bài 19). Để giải quyết bài toán: Không cần sắp xếp từ điển mà chỉ duyệt qua từng từ, kiểm tra nó có phải subsequence không. Sau đó, so sánh để tìm từ có chiều dài lớn nhất và thứ tự từ điển nhỏ nhất.
**Mã nguồn Java:**
```java
import java.util.List;

public class LongestWordInDictionary {
    public String findLongestWord(String s, List<String> dictionary) {
        String longest = "";
        
        for (String word : dictionary) {
            // Kiểm tra Is Subsequence
            if (isSubsequence(word, s)) {
                // Cập nhật kết quả nếu dài hơn, hoặc độ dài bằng nhưng thứ tự từ điển nhỏ hơn
                if (word.length() > longest.length() || 
                   (word.length() == longest.length() && word.compareTo(longest) < 0)) {
                    longest = word;
                }
            }
        }
        
        return longest;
    }
    
    private boolean isSubsequence(String word, String s) {
        int i = 0, j = 0;
        while (i < word.length() && j < s.length()) {
            if (word.charAt(i) == s.charAt(j)) {
                i++;
            }
            j++;
        }
        return i == word.length();
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(K \cdot M)$ (với $K$ là số từ trong từ điển, $M$ là chiều dài `s`), Space $\mathcal{O}(1)$.
