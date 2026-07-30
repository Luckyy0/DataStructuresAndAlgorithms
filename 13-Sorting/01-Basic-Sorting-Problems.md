# 01. Basic Sorting Problems

## Danh sách bài tập
1. Sort an Array (Insertion Sort approach)
2. Insertion Sort List
3. Sort Colors (Selection sort concept)
4. Wiggle Sort
5. Third Maximum Number
6. Maximum Product of Three Numbers
7. Height Checker
8. Relative Sort Array
9. Sort Array By Parity
10. Sort Array By Parity II

---

### 1. Sort an Array (Insertion Sort approach)
**Đề bài chi tiết:** Cho một mảng các số nguyên `nums`, hãy sắp xếp mảng theo thứ tự tăng dần. (Trong bài này chúng ta áp dụng thuật toán Insertion Sort để hiểu rõ cơ chế, dù có thể gặp Time Limit Exceeded với tập dữ liệu rất lớn).
**Phân tích thuật toán:** Sử dụng Insertion Sort để duyệt qua từng phần tử và chèn nó vào vị trí thích hợp trong mảng con đã được sắp xếp sẵn ở phần đầu mảng.
**Mã nguồn Java:**
```java
class Solution {
    public int[] sortArray(int[] nums) {
        // Insertion Sort implementation
        for (int i = 1; i < nums.length; i++) {
            int key = nums[i];
            int j = i - 1;
            while (j >= 0 && nums[j] > key) {
                nums[j + 1] = nums[j];
                j--;
            }
            nums[j + 1] = key;
        }
        return nums;
    }
}
```
**Độ phức tạp:** Thời gian: O(N^2), Không gian: O(1).

### 2. Insertion Sort List
**Đề bài chi tiết:** Sắp xếp một danh sách liên kết đơn (Singly Linked List) sử dụng thuật toán Insertion Sort.
**Phân tích thuật toán:** Duyệt qua danh sách bằng một con trỏ hiện tại. Ở mỗi bước, tách con trỏ đó ra khỏi danh sách chưa sắp xếp và chèn nó vào đúng vị trí trong danh sách con đã được sắp xếp sẵn. Dùng một `dummy` node để giúp thao tác chèn ở vị trí đầu dễ dàng.
**Mã nguồn Java:**
```java
class Solution {
    public ListNode insertionSortList(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode dummy = new ListNode(0);
        ListNode curr = head;
        
        while (curr != null) {
            ListNode prev = dummy;
            // Tìm vị trí để chèn
            while (prev.next != null && prev.next.val < curr.val) {
                prev = prev.next;
            }
            ListNode nextTemp = curr.next;
            // Chèn curr vào giữa prev và prev.next
            curr.next = prev.next;
            prev.next = curr;
            curr = nextTemp;
        }
        return dummy.next;
    }
}
```
**Độ phức tạp:** Thời gian: O(N^2), Không gian: O(1).

### 3. Sort Colors
**Đề bài chi tiết:** Cho một mảng `nums` chứa n phần tử đại diện cho các màu đỏ, trắng và xanh dương, lần lượt được biểu diễn bằng 0, 1 và 2. Hãy sắp xếp mảng in-place sao cho các màu đồng nhất đứng kề nhau theo thứ tự đỏ, trắng, xanh.
**Phân tích thuật toán:** Sử dụng thuật toán Dutch National Flag với 3 con trỏ `low`, `mid`, `high`. Duyệt `mid` từ đầu đến cuối: gặp 0 thì đổi chỗ cho `low` và nhích con trỏ, gặp 2 thì đổi chỗ cho `high` và lùi con trỏ `high`, gặp 1 thì nhích con trỏ `mid`. Tư tưởng hoán vị in-place này là phiên bản mở rộng của Selection Sort.
**Mã nguồn Java:**
```java
class Solution {
    public void sortColors(int[] nums) {
        int low = 0, mid = 0, high = nums.length - 1;
        while (mid <= high) {
            if (nums[mid] == 0) {
                swap(nums, low, mid);
                low++; mid++;
            } else if (nums[mid] == 1) {
                mid++;
            } else {
                swap(nums, mid, high);
                high--;
            }
        }
    }
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
    }
}
```
**Độ phức tạp:** Thời gian: O(N), Không gian: O(1).

### 4. Wiggle Sort
**Đề bài chi tiết:** Cho một mảng số nguyên chưa sắp xếp `nums`, hãy sắp xếp lại in-place theo điều kiện định dạng lượn sóng: `nums[0] <= nums[1] >= nums[2] <= nums[3]...`.
**Phân tích thuật toán:** Duyệt qua mảng một lần, kiểm tra các index kề nhau. Ở index chẵn, phần tử phải nhỏ hơn hoặc bằng phần tử tiếp theo. Ở index lẻ, phần tử phải lớn hơn hoặc bằng phần tử tiếp theo. Nếu sai, ta lập tức hoán đổi vị trí của chúng. Cách thao tác liền kề này mượn tư tưởng của Bubble Sort.
**Mã nguồn Java:**
```java
class Solution {
    public void wiggleSort(int[] nums) {
        for (int i = 0; i < nums.length - 1; i++) {
            if (((i % 2 == 0) && nums[i] > nums[i + 1]) ||
                ((i % 2 == 1) && nums[i] < nums[i + 1])) {
                int temp = nums[i];
                nums[i] = nums[i + 1];
                nums[i + 1] = temp;
            }
        }
    }
}
```
**Độ phức tạp:** Thời gian: O(N), Không gian: O(1).

### 5. Third Maximum Number
**Đề bài chi tiết:** Cho mảng số nguyên `nums`, hãy trả về số nguyên lớn thứ ba không trùng lặp có trong mảng. Nếu không tồn tại số lớn thứ ba, hãy trả về giá trị lớn nhất.
**Phân tích thuật toán:** Duy trì ba biến để lưu giá trị lớn nhất, lớn thứ hai và lớn thứ ba. Duyệt qua mảng và liên tục cập nhật ba biến này. Tư tưởng tìm cực trị (Max/Min) từng phần này chính là cốt lõi của Selection Sort.
**Mã nguồn Java:**
```java
class Solution {
    public int thirdMax(int[] nums) {
        Integer max1 = null, max2 = null, max3 = null;
        for (Integer n : nums) {
            if (n.equals(max1) || n.equals(max2) || n.equals(max3)) continue;
            if (max1 == null || n > max1) {
                max3 = max2; max2 = max1; max1 = n;
            } else if (max2 == null || n > max2) {
                max3 = max2; max2 = n;
            } else if (max3 == null || n > max3) {
                max3 = n;
            }
        }
        return max3 == null ? max1 : max3;
    }
}
```
**Độ phức tạp:** Thời gian: O(N), Không gian: O(1).

### 6. Maximum Product of Three Numbers
**Đề bài chi tiết:** Cho một mảng nguyên `nums`, hãy tìm ba số bất kỳ có tích lớn nhất và trả về giá trị tích đó.
**Phân tích thuật toán:** Giá trị lớn nhất có thể nằm ở tích của 3 số dương lớn nhất, hoặc 2 số âm có giá trị tuyệt đối lớn nhất kết hợp với 1 số dương lớn nhất. Việc này có thể thực hiện thông qua sắp xếp mảng và so sánh 2 khả năng biên: `nums[n-1]*nums[n-2]*nums[n-3]` với `nums[0]*nums[1]*nums[n-1]`.
**Mã nguồn Java:**
```java
class Solution {
    public int maximumProduct(int[] nums) {
        Arrays.sort(nums); // Thường sử dụng thư viện nội bộ kết hợp Insertion/Dual-Pivot QuickSort
        int n = nums.length;
        int product1 = nums[n-1] * nums[n-2] * nums[n-3];
        int product2 = nums[0] * nums[1] * nums[n-1];
        return Math.max(product1, product2);
    }
}
```
**Độ phức tạp:** Thời gian: O(N log N) (hoặc có thể rút gọn thành O(N) nếu tìm 5 trị số cực hạn). Không gian: O(1) space complexity.

### 7. Height Checker
**Đề bài chi tiết:** Một lớp học cần xếp học sinh thành hàng thẳng theo chiều cao tăng dần. Trả về số lượng chỉ số `i` mà tại đó `heights[i] != expected[i]` với `expected` là mảng sau khi sắp xếp chuẩn.
**Phân tích thuật toán:** Tạo một bản sao của mảng hiện tại, sau đó sử dụng bất kỳ thuật toán Sorting nào (Arrays.sort) lên bản sao đó. Duyệt đồng thời mảng gốc và mảng đã sort để đếm các khác biệt.
**Mã nguồn Java:**
```java
class Solution {
    public int heightChecker(int[] heights) {
        int[] expected = heights.clone();
        Arrays.sort(expected);
        int count = 0;
        for (int i = 0; i < heights.length; i++) {
            if (heights[i] != expected[i]) {
                count++;
            }
        }
        return count;
    }
}
```
**Độ phức tạp:** Thời gian: O(N log N), Không gian: O(N) dành cho mảng copy.

### 8. Relative Sort Array
**Đề bài chi tiết:** Cho hai mảng `arr1` và `arr2`, trong đó `arr2` chứa các phần tử hoàn toàn phân biệt và mọi phần tử của `arr2` đều nằm trong `arr1`. Sắp xếp mảng `arr1` sao cho thứ tự tương đối các phần tử giống với thứ tự trong `arr2`. Các phần tử không nằm trong `arr2` thì sắp xếp tăng dần ở cuối.
**Phân tích thuật toán:** Do giới hạn số lượng nhỏ, có thể áp dụng Counting Sort (mở rộng của sắp xếp tuyến tính). Đếm tần số xuất hiện các phần tử, ghi đè mảng ban đầu dựa theo thứ tự yêu cầu từ `arr2` và sau đó điền các giá trị còn lại theo thứ tự tăng dần.
**Mã nguồn Java:**
```java
class Solution {
    public int[] relativeSortArray(int[] arr1, int[] arr2) {
        int[] count = new int[1001];
        for (int n : arr1) count[n]++;
        int i = 0;
        for (int n : arr2) {
            while (count[n]-- > 0) {
                arr1[i++] = n;
            }
        }
        for (int n = 0; n < count.length; n++) {
            while (count[n]-- > 0) {
                arr1[i++] = n;
            }
        }
        return arr1;
    }
}
```
**Độ phức tạp:** Thời gian: O(N + K) với K là phạm vi trị số (1001), Không gian: O(K).

### 9. Sort Array By Parity
**Đề bài chi tiết:** Cho một mảng nguyên `nums`, sắp xếp và chuyển đổi mảng đó sao cho tất cả các phần tử chẵn đứng trước tất cả các phần tử lẻ. Bạn có thể trả về mảng kết quả dưới bất kỳ thứ tự hợp lệ nào.
**Phân tích thuật toán:** Sử dụng hai con trỏ, `left` bắt đầu từ 0 và `right` từ đuôi mảng. Nếu `nums[left]` lẻ và `nums[right]` chẵn, hoán vị chúng. Quá trình chia rẽ này tương tự thuật toán Partition của QuickSort (Sắp xếp phân vùng).
**Mã nguồn Java:**
```java
class Solution {
    public int[] sortArrayByParity(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            if (nums[left] % 2 > nums[right] % 2) {
                int temp = nums[left];
                nums[left] = nums[right];
                nums[right] = temp;
            }
            if (nums[left] % 2 == 0) left++;
            if (nums[right] % 2 == 1) right--;
        }
        return nums;
    }
}
```
**Độ phức tạp:** Thời gian: O(N), Không gian: O(1).

### 10. Sort Array By Parity II
**Đề bài chi tiết:** Cho mảng `nums` chứa số nguyên, một nửa là số chẵn, nửa còn lại là số lẻ. Sắp xếp mảng in-place sao cho khi phần tử ở index `i` là chẵn, `nums[i]` cũng chẵn, và khi `i` là lẻ thì `nums[i]` lẻ.
**Phân tích thuật toán:** Duyệt qua mảng bằng hai con trỏ `i` đếm cho vị trí chẵn và `j` đếm vị trí lẻ. Tìm ra phần tử không đúng tính chẵn lẻ tại từng con trỏ và hoán đổi chúng lại với nhau.
**Mã nguồn Java:**
```java
class Solution {
    public int[] sortArrayByParityII(int[] nums) {
        int i = 0, j = 1;
        int n = nums.length;
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
**Độ phức tạp:** Thời gian: O(N), Không gian: O(1).

---

### 11. Minimum Absolute Difference
**Đề bài chi tiết:** Cho một mảng các số nguyên phân biệt `arr`. Tìm tất cả các cặp phần tử có độ chênh lệch tuyệt đối nhỏ nhất có thể. Trả về danh sách các cặp này theo thứ tự tăng dần.
**Phân tích thuật toán:** Đầu tiên, sắp xếp mảng. Sự chênh lệch tuyệt đối nhỏ nhất chỉ có thể xảy ra giữa hai phần tử kề nhau trong mảng đã sắp xếp. Duyệt qua mảng để tìm độ chênh lệch nhỏ nhất, sau đó duyệt lại lần nữa (hoặc lưu trong cùng một lần duyệt) để gom tất cả các cặp có độ chênh lệch bằng độ chênh lệch nhỏ nhất đó.
**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> minimumAbsDifference(int[] arr) {
        Arrays.sort(arr);
        List<List<Integer>> result = new ArrayList<>();
        int minDiff = Integer.MAX_VALUE;
        
        for (int i = 0; i < arr.length - 1; i++) {
            int diff = arr[i + 1] - arr[i];
            if (diff < minDiff) {
                minDiff = diff;
                result.clear();
                result.add(Arrays.asList(arr[i], arr[i + 1]));
            } else if (diff == minDiff) {
                result.add(Arrays.asList(arr[i], arr[i + 1]));
            }
        }
        return result;
    }
}
```
**Độ phức tạp:** Thời gian: O(N log N) do phải sắp xếp. Không gian: O(1) hoặc O(N) tùy thuộc vào thuật toán sắp xếp bên dưới và cấu trúc lưu kết quả.

### 12. Assign Cookies
**Đề bài chi tiết:** Giả sử bạn là một người phụ huynh tuyệt vời và muốn phát bánh quy cho những đứa trẻ của mình. Mỗi đứa trẻ `i` có một mức độ tham lam `g[i]`, là kích thước tối thiểu của một chiếc bánh quy mà đứa trẻ đó sẽ hài lòng. Mỗi chiếc bánh quy `j` có kích thước `s[j]`. Nếu `s[j] >= g[i]`, chúng ta có thể giao bánh quy `j` cho trẻ `i`. Tìm số lượng trẻ em lớn nhất có thể hài lòng.
**Phân tích thuật toán:** Áp dụng Greedy. Sắp xếp mảng độ tham lam của trẻ em và mảng kích thước bánh quy. Dùng hai con trỏ, một trỏ tới trẻ em và một trỏ tới bánh quy. Nếu bánh quy hiện tại đủ lớn để thỏa mãn đứa trẻ hiện tại, ta cho đứa trẻ đó bánh quy và chuyển sang đứa trẻ tiếp theo. Nếu không, ta thử bánh quy tiếp theo lớn hơn.
**Mã nguồn Java:**
```java
class Solution {
    public int findContentChildren(int[] g, int[] s) {
        Arrays.sort(g);
        Arrays.sort(s);
        int i = 0, j = 0;
        
        while (i < g.length && j < s.length) {
            if (s[j] >= g[i]) {
                i++; // Trẻ em i đã được thỏa mãn
            }
            j++; // Chuyển sang bánh quy tiếp theo
        }
        return i;
    }
}
```
**Độ phức tạp:** Thời gian: O(N log N + M log M) với N, M là chiều dài 2 mảng. Không gian: O(1).

### 13. Intersection of Two Arrays
**Đề bài chi tiết:** Cho hai mảng số nguyên `nums1` và `nums2`, trả về một mảng chứa sự giao nhau (intersection) của chúng. Mỗi phần tử trong kết quả phải là duy nhất và bạn có thể trả về kết quả theo bất kỳ thứ tự nào.
**Phân tích thuật toán:** Mặc dù có thể dùng HashSet, ta có thể áp dụng thuật toán sắp xếp cả hai mảng sau đó dùng kỹ thuật hai con trỏ (Two Pointers) để duyệt đồng thời. Nếu `nums1[i] < nums2[j]` thì tăng `i`, ngược lại tăng `j`. Nếu bằng nhau, đưa vào kết quả (đảm bảo không trùng lặp) và tăng cả hai.
**Mã nguồn Java:**
```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
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
                if (list.isEmpty() || list.get(list.size() - 1) != nums1[i]) {
                    list.add(nums1[i]);
                }
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
**Độ phức tạp:** Thời gian: O(N log N + M log M). Không gian: O(min(N, M)) cho danh sách kết quả (hoặc không gian thuật toán sắp xếp).

### 14. Valid Anagram
**Đề bài chi tiết:** Cho hai chuỗi `s` và `t`, trả về `true` nếu `t` là anagram của `s`, và `false` nếu không phải. Một anagram là một từ hay cụm từ được tạo thành bằng cách sắp xếp lại các chữ cái của một từ hoặc cụm từ khác, thông thường là sử dụng tất cả các chữ cái gốc chính xác một lần.
**Phân tích thuật toán:** Một cách đơn giản sử dụng Sorting là chuyển đổi cả 2 chuỗi thành mảng ký tự `char[]`, sau đó sắp xếp hai mảng này. Nếu sau khi sắp xếp, 2 mảng giống hệt nhau, thì chuỗi này là anagram của chuỗi kia.
**Mã nguồn Java:**
```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }
        char[] sChars = s.toCharArray();
        char[] tChars = t.toCharArray();
        Arrays.sort(sChars);
        Arrays.sort(tChars);
        return Arrays.equals(sChars, tChars);
    }
}
```
**Độ phức tạp:** Thời gian: O(N log N) với N là chiều dài chuỗi. Không gian: O(N) để lưu mảng ký tự.

### 15. Largest Perimeter Triangle
**Đề bài chi tiết:** Cho một mảng các số nguyên dương `nums`, trả về chu vi lớn nhất có thể tạo thành một tam giác có diện tích khác không từ 3 độ dài có trong mảng. Nếu không thể tạo thành bất kỳ tam giác nào, trả về `0`.
**Phân tích thuật toán:** Sắp xếp mảng theo thứ tự tăng dần. Một bộ 3 cạnh có thể tạo thành tam giác nếu tổng 2 cạnh nhỏ lớn hơn cạnh lớn nhất. Duyệt ngược từ cuối mảng (để ưu tiên lấy cạnh lớn nhất trước), kiểm tra điều kiện `nums[i-2] + nums[i-1] > nums[i]`. Nếu thỏa mãn, đó chính là tam giác có chu vi lớn nhất.
**Mã nguồn Java:**
```java
class Solution {
    public int largestPerimeter(int[] nums) {
        Arrays.sort(nums);
        for (int i = nums.length - 1; i >= 2; i--) {
            if (nums[i - 2] + nums[i - 1] > nums[i]) {
                return nums[i - 2] + nums[i - 1] + nums[i];
            }
        }
        return 0;
    }
}
```
**Độ phức tạp:** Thời gian: O(N log N). Không gian: O(1).

### 16. Maximum Units on a Truck
**Đề bài chi tiết:** Bạn được giao nhiệm vụ xếp hàng lên một chiếc xe tải có giới hạn số thùng hàng tối đa là `truckSize`. Cho mảng 2D `boxTypes`, trong đó `boxTypes[i] = [numberOfBoxesi, numberOfUnitsPerBoxi]`. Chọn các thùng hàng sao cho tổng số đơn vị (units) trên xe tải là lớn nhất.
**Phân tích thuật toán:** Thuật toán Greedy. Sắp xếp mảng `boxTypes` theo số lượng đơn vị trong mỗi thùng (`numberOfUnitsPerBoxi`) giảm dần. Cố gắng bốc lên xe càng nhiều thùng hàng có đơn vị cao càng tốt cho đến khi đầy xe tải.
**Mã nguồn Java:**
```java
class Solution {
    public int maximumUnits(int[][] boxTypes, int truckSize) {
        Arrays.sort(boxTypes, (a, b) -> Integer.compare(b[1], a[1]));
        int maxUnits = 0;
        
        for (int[] boxType : boxTypes) {
            int boxesToTake = Math.min(truckSize, boxType[0]);
            maxUnits += boxesToTake * boxType[1];
            truckSize -= boxesToTake;
            if (truckSize == 0) break;
        }
        return maxUnits;
    }
}
```
**Độ phức tạp:** Thời gian: O(N log N) do sắp xếp. Không gian: O(1) hoặc O(log N) tuỳ thuộc vào thuật toán sắp xếp.

### 17. Squares of a Sorted Array
**Đề bài chi tiết:** Cho một mảng số nguyên `nums` được sắp xếp theo thứ tự không giảm, trả về mảng bình phương của mỗi phần tử, cũng được sắp xếp theo thứ tự không giảm.
**Phân tích thuật toán:** Mảng ban đầu có thể chứa số âm. Khi bình phương, các số âm nhỏ nhất (giá trị tuyệt đối lớn nhất) sẽ trở thành số dương lớn nhất. Sử dụng kỹ thuật hai con trỏ ở hai đầu mảng `left` và `right`, so sánh bình phương của chúng. Đưa giá trị lớn hơn vào mảng kết quả từ phải sang trái.
**Mã nguồn Java:**
```java
class Solution {
    public int[] sortedSquares(int[] nums) {
        int n = nums.length;
        int[] result = new int[n];
        int left = 0, right = n - 1;
        int index = n - 1;
        
        while (left <= right) {
            int leftSquare = nums[left] * nums[left];
            int rightSquare = nums[right] * nums[right];
            if (leftSquare > rightSquare) {
                result[index--] = leftSquare;
                left++;
            } else {
                result[index--] = rightSquare;
                right--;
            }
        }
        return result;
    }
}
```
**Độ phức tạp:** Thời gian: O(N) do duyệt 1 vòng duy nhất bằng 2 con trỏ. Không gian: O(N) để lưu mảng kết quả.

### 18. Make Two Arrays Equal by Reversing Sub-arrays
**Đề bài chi tiết:** Bạn được cho hai mảng số nguyên `target` và `arr` có cùng độ dài. Trong một bước, bạn có thể chọn bất kỳ mảng con nào không rỗng của `arr` và đảo ngược nó. Bạn có thể thực hiện bao nhiêu bước tùy ý. Trả về `true` nếu bạn có thể biến `arr` thành `target`, hoặc `false` nếu không thể.
**Phân tích thuật toán:** Có thể chứng minh được rằng nếu hai mảng có chứa tất cả các phần tử giống nhau với cùng tần suất xuất hiện, ta luôn có thể biến `arr` thành `target` bằng cách đảo ngược các mảng con (việc này tương tự như các phép hoán đổi liên tiếp trong Bubble Sort). Vì vậy, cách đơn giản là sắp xếp cả hai mảng và kiểm tra xem chúng có giống hệt nhau không.
**Mã nguồn Java:**
```java
class Solution {
    public boolean canBeEqual(int[] target, int[] arr) {
        Arrays.sort(target);
        Arrays.sort(arr);
        return Arrays.equals(target, arr);
    }
}
```
**Độ phức tạp:** Thời gian: O(N log N) để sắp xếp cả hai mảng. Không gian: O(1) hoặc O(log N) tuỳ thuộc vào thuật toán sắp xếp của hệ thống.

### 19. Array Partition I
**Đề bài chi tiết:** Cho một mảng số nguyên `nums` có `2n` phần tử, hãy chia các phần tử thành `n` cặp `(a1, b1), (a2, b2), ..., (an, bn)` sao cho tổng `min(ai, bi)` của tất cả các cặp là lớn nhất. Trả về tổng lớn nhất này.
**Phân tích thuật toán:** Để tối đa hóa tổng các giá trị nhỏ nhất của các cặp, chúng ta không nên ghép một số lớn với một số rất nhỏ, vì giá trị lớn sẽ bị "lãng phí". Phương pháp tối ưu là sắp xếp mảng và ghép các số gần nhau lại với nhau. Kết quả cần tìm chính là tổng của các phần tử ở các vị trí có index chẵn trong mảng đã sắp xếp.
**Mã nguồn Java:**
```java
class Solution {
    public int arrayPairSum(int[] nums) {
        Arrays.sort(nums);
        int sum = 0;
        for (int i = 0; i < nums.length; i += 2) {
            sum += nums[i];
        }
        return sum;
    }
}
```
**Độ phức tạp:** Thời gian: O(N log N) cho việc sắp xếp mảng. Không gian: O(1).

### 20. Largest Number At Least Twice of Others
**Đề bài chi tiết:** Cho một mảng nguyên `nums`, luôn có một phần tử lớn nhất duy nhất. Hãy kiểm tra xem phần tử lớn nhất đó có lớn hơn hoặc bằng gấp đôi so với mọi số khác trong mảng hay không. Nếu có, trả về index của nó, ngược lại trả về `-1`.
**Phân tích thuật toán:** Bài toán yêu cầu tính chất của phần tử lớn nhất so với phần tử lớn thứ hai, do đó chỉ cần tìm ra số lớn nhất và số lớn thứ hai trong mảng. Nếu số lớn nhất lớn hơn hoặc bằng 2 lần số lớn thứ hai, thì tự nhiên nó sẽ thỏa mãn điều kiện đó với tất cả các số nhỏ hơn khác. Bài toán này chia sẻ tư tưởng cốt lõi tìm giá trị cực trị của thuật toán Selection Sort.
**Mã nguồn Java:**
```java
class Solution {
    public int dominantIndex(int[] nums) {
        int maxIndex = -1;
        int max = -1;
        int secondMax = -1;
        
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] > max) {
                secondMax = max;
                max = nums[i];
                maxIndex = i;
            } else if (nums[i] > secondMax) {
                secondMax = nums[i];
            }
        }
        
        if (max >= secondMax * 2) {
            return maxIndex;
        }
        return -1;
    }
}
```
**Độ phức tạp:** Thời gian: O(N) do chỉ cần duyệt mảng một lần. Không gian: O(1).

---

### 21. Find the Difference
**Đề bài chi tiết:** Cho hai chuỗi `s` và `t`. Chuỗi `t` được tạo ra bằng cách xáo trộn các ký tự của chuỗi `s` và thêm vào một ký tự ngẫu nhiên ở một vị trí bất kỳ. Hãy tìm ký tự đã được thêm vào `t`.
**Phân tích thuật toán:** Một cách tiếp cận dựa trên Sorting là chuyển hai chuỗi thành mảng ký tự và sắp xếp chúng. Sau đó, duyệt đồng thời hai mảng từ đầu đến cuối. Vị trí đầu tiên mà hai ký tự khác nhau chính là ký tự được thêm vào. Nếu duyệt hết chuỗi `s` mà không thấy khác biệt, thì ký tự cuối cùng của chuỗi `t` chính là ký tự được thêm vào.
**Mã nguồn Java:**
```java
class Solution {
    public char findTheDifference(String s, String t) {
        char[] sChars = s.toCharArray();
        char[] tChars = t.toCharArray();
        Arrays.sort(sChars);
        Arrays.sort(tChars);
        
        for (int i = 0; i < sChars.length; i++) {
            if (sChars[i] != tChars[i]) {
                return tChars[i];
            }
        }
        return tChars[tChars.length - 1];
    }
}
```
**Độ phức tạp:** Thời gian: O(N log N) do thao tác sắp xếp. Không gian: O(N) để lưu trữ mảng ký tự.

### 22. Kth Largest Element in a Stream
**Đề bài chi tiết:** Thiết kế một lớp để tìm phần tử lớn thứ `k` trong một dòng dữ liệu. Lưu ý rằng đó là phần tử lớn thứ `k` theo thứ tự đã sắp xếp, chứ không phải phần tử duy nhất lớn thứ `k`. Lớp `KthLargest` khởi tạo với số nguyên `k` và một mảng số nguyên `nums`.
**Phân tích thuật toán:** Thay vì sử dụng mảng tĩnh và sắp xếp lại mỗi lần có phần tử mới (Insertion Sort tốn O(N) cho mỗi lần thêm), chúng ta sử dụng cấu trúc dữ liệu Min-Heap (`PriorityQueue` trong Java). Heap sẽ duy trì kích thước tối đa là `k`. Khi thêm phần tử mới, nếu Heap nhỏ hơn `k`, ta thêm vào. Nếu Heap đã đủ `k` phần tử và phần tử mới lớn hơn phần tử nhỏ nhất trong Heap (phần tử ở đỉnh Heap), ta loại bỏ đỉnh và thêm phần tử mới vào. Phần tử ở đỉnh Heap luôn là phần tử lớn thứ `k`.
**Mã nguồn Java:**
```java
class KthLargest {
    private PriorityQueue<Integer> minHeap;
    private int k;

    public KthLargest(int k, int[] nums) {
        this.k = k;
        minHeap = new PriorityQueue<>(k);
        for (int num : nums) {
            add(num);
        }
    }
    
    public int add(int val) {
        if (minHeap.size() < k) {
            minHeap.offer(val);
        } else if (val > minHeap.peek()) {
            minHeap.poll();
            minHeap.offer(val);
        }
        return minHeap.peek();
    }
}
```
**Độ phức tạp:** Thời gian: O(N log K) cho khởi tạo và O(log K) cho mỗi thao tác `add`. Không gian: O(K) cho Min-Heap.

### 23. Contains Duplicate
**Đề bài chi tiết:** Cho một mảng số nguyên `nums`, trả về `true` nếu có ít nhất một giá trị xuất hiện ít nhất hai lần trong mảng và trả về `false` nếu tất cả các phần tử đều phân biệt.
**Phân tích thuật toán:** Một cách giải quyết là sắp xếp mảng. Sau khi mảng được sắp xếp, bất kỳ phần tử nào trùng lặp đều sẽ nằm kề nhau. Do đó, ta chỉ cần duyệt qua mảng và kiểm tra xem có phần tử nào giống với phần tử ngay trước nó hay không. Mặc dù HashSet cho thời gian tốt hơn `O(N)`, Sorting cung cấp giải pháp `O(1)` không gian phụ trợ (nếu không tính không gian cho thuật toán sắp xếp).
**Mã nguồn Java:**
```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        Arrays.sort(nums);
        for (int i = 0; i < nums.length - 1; i++) {
            if (nums[i] == nums[i + 1]) {
                return true;
            }
        }
        return false;
    }
}
```
**Độ phức tạp:** Thời gian: O(N log N) cho việc sắp xếp mảng. Không gian: O(1) hoặc O(log N).

### 24. Missing Number
**Đề bài chi tiết:** Cho một mảng `nums` chứa `n` số nguyên phân biệt trong khoảng `[0, n]`, trả về số duy nhất trong khoảng đó bị thiếu khỏi mảng.
**Phân tích thuật toán:** Bằng cách sắp xếp mảng, ta mong đợi phần tử tại chỉ số `i` sẽ có giá trị là `i`. Ta có thể duyệt mảng đã sắp xếp và trả về chỉ số đầu tiên mà giá trị của nó không khớp với chỉ số đó. Nếu tất cả đều khớp, số bị thiếu chính là `n`. Có nhiều cách tiếp cận tối ưu hơn như toán tử XOR hoặc tổng Gauss, nhưng đây là cách tiếp cận bằng Sorting.
**Mã nguồn Java:**
```java
class Solution {
    public int missingNumber(int[] nums) {
        Arrays.sort(nums);
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != i) {
                return i;
            }
        }
        return nums.length;
    }
}
```
**Độ phức tạp:** Thời gian: O(N log N). Không gian: O(1) hoặc O(log N).

### 25. Set Mismatch
**Đề bài chi tiết:** Bạn có một tập hợp các số nguyên từ `1` đến `n` ban đầu chứa tất cả các số phân biệt. Tuy nhiên, do lỗi dữ liệu, một trong các số trong tập hợp bị trùng lặp với một số khác trong tập hợp, dẫn đến việc lặp lại một số và mất đi một số khác. Cho mảng `nums` đại diện cho trạng thái dữ liệu lỗi này, hãy tìm số bị lặp và số bị thiếu.
**Phân tích thuật toán:** Nếu chúng ta sắp xếp mảng, số bị lặp sẽ xuất hiện cạnh nhau. Chúng ta có thể duyệt qua mảng để tìm số bị lặp. Số bị thiếu có thể được tìm thấy bằng cách theo dõi sự chênh lệch giữa các số liên tiếp (nếu chênh lệch là 2 thì số ở giữa bị thiếu) hoặc so sánh tổng. Cách tốt nhất dùng sorting kết hợp đếm hoặc so sánh các phần tử kề nhau.
**Mã nguồn Java:**
```java
class Solution {
    public int[] findErrorNums(int[] nums) {
        Arrays.sort(nums);
        int dup = -1;
        int missing = 1;
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] == nums[i - 1]) {
                dup = nums[i];
            } else if (nums[i] > nums[i - 1] + 1) {
                missing = nums[i - 1] + 1;
            }
        }
        if (nums[nums.length - 1] != nums.length) {
            missing = nums.length;
        }
        return new int[]{dup, missing};
    }
}
```
**Độ phức tạp:** Thời gian: O(N log N) để sắp xếp. Không gian: O(1) hoặc O(log N).

### 26. Find All Numbers Disappeared in an Array
**Đề bài chi tiết:** Cho một mảng `nums` gồm `n` số nguyên trong khoảng `[1, n]`, trả về một mảng chứa tất cả các số nguyên nằm trong khoảng `[1, n]` không xuất hiện trong `nums`. (Giải thuật tối ưu không sử dụng thêm bộ nhớ phụ ngoại trừ danh sách trả về).
**Phân tích thuật toán:** Mặc dù không dùng Sorting truyền thống, ta có thể ứng dụng tư tưởng Cycle Sort. Chúng ta sẽ "đặt" mỗi phần tử về đúng vị trí mong đợi của nó (phần tử có giá trị `x` thì nên ở vị trí `x - 1`). Ta lặp qua mảng, và đổi chỗ phần tử hiện tại tới đúng vị trí của nó. Sau khi hoàn thành việc đổi chỗ toàn mảng, ta duyệt thêm một lần nữa; vị trí nào chứa phần tử không đúng (tức là `nums[i] != i + 1`) thì số `i + 1` chính là số bị thiếu.
**Mã nguồn Java:**
```java
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        int i = 0;
        while (i < nums.length) {
            int correctIndex = nums[i] - 1;
            if (nums[i] != nums[correctIndex]) {
                // Hoán đổi
                int temp = nums[i];
                nums[i] = nums[correctIndex];
                nums[correctIndex] = temp;
            } else {
                i++;
            }
        }
        
        List<Integer> result = new ArrayList<>();
        for (int j = 0; j < nums.length; j++) {
            if (nums[j] != j + 1) {
                result.add(j + 1);
            }
        }
        return result;
    }
}
```
**Độ phức tạp:** Thời gian: O(N) do mỗi phần tử chỉ bị đổi chỗ tối đa một lần trong Cycle Sort. Không gian: O(1).

### 27. Single Number
**Đề bài chi tiết:** Cho một mảng số nguyên không rỗng `nums`, mọi phần tử xuất hiện hai lần ngoại trừ một phần tử xuất hiện duy nhất một lần. Hãy tìm phần tử duy nhất đó. Bạn phải triển khai một giải pháp có độ phức tạp thời gian tuyến tính và sử dụng không gian tĩnh `O(1)`.
**Phân tích thuật toán:** Một trong các cách thông dụng nếu không dùng XOR là sắp xếp mảng. Sau khi mảng được sắp xếp, các phần tử giống nhau sẽ nằm kề nhau theo từng cặp. Ta duyệt mảng với bước nhảy là `2`. Nếu `nums[i] != nums[i+1]`, thì `nums[i]` chính là phần tử duy nhất. Dù không đáp ứng thời gian tuyến tính thuần, nhưng giúp hình thành tư duy sắp xếp gom nhóm.
**Mã nguồn Java:**
```java
class Solution {
    public int singleNumber(int[] nums) {
        Arrays.sort(nums);
        for (int i = 0; i < nums.length - 1; i += 2) {
            if (nums[i] != nums[i + 1]) {
                return nums[i];
            }
        }
        return nums[nums.length - 1]; // Phần tử lẻ loi nằm ở cuối
    }
}
```
**Độ phức tạp:** Thời gian: O(N log N) cho sắp xếp. Không gian: O(1) hoặc O(log N). (Lời giải tối ưu dùng `XOR` cho thời gian `O(N)`).

### 28. Find All Duplicates in an Array
**Đề bài chi tiết:** Cho một mảng nguyên `nums` có độ dài `n` với các phần tử nằm trong khoảng `[1, n]`. Mỗi số nguyên có thể xuất hiện một lần hoặc hai lần, trả về một mảng chứa tất cả các số nguyên xuất hiện hai lần. Yêu cầu viết thuật toán chạy trong `O(n)` thời gian và dùng `O(1)` không gian bổ sung.
**Phân tích thuật toán:** Tương tự như bài 26, áp dụng Cycle Sort là một dạng sắp xếp in-place tuyến tính. Chúng ta cố gắng đưa phần tử `x` về vị trí `x - 1`. Trong quá trình hoán đổi, nếu phát hiện phần tử tại vị trí đích đã mang giá trị đúng bằng nó, thì ta bỏ qua (tránh lặp vô hạn). Duyệt lại lần cuối, tất cả những vị trí `i` mà `nums[i] != i + 1` chính là các số bị trùng lặp.
**Mã nguồn Java:**
```java
class Solution {
    public List<Integer> findDuplicates(int[] nums) {
        List<Integer> result = new ArrayList<>();
        int i = 0;
        while (i < nums.length) {
            int correctIndex = nums[i] - 1;
            if (nums[i] != nums[correctIndex]) {
                int temp = nums[i];
                nums[i] = nums[correctIndex];
                nums[correctIndex] = temp;
            } else {
                i++;
            }
        }
        
        for (int j = 0; j < nums.length; j++) {
            if (nums[j] != j + 1) {
                result.add(nums[j]);
            }
        }
        return result;
    }
}
```
**Độ phức tạp:** Thời gian: O(N) nhờ Cycle Sort. Không gian: O(1).

### 29. First Missing Positive
**Đề bài chi tiết:** Cho một mảng số nguyên chưa sắp xếp `nums`, trả về số nguyên dương nhỏ nhất không xuất hiện trong mảng. Yêu cầu thời gian chạy `O(n)` và bộ nhớ tĩnh `O(1)`.
**Phân tích thuật toán:** Đây là ứng dụng kinh điển của Cycle Sort. Mục tiêu là sắp xếp các phần tử dương sao cho số nguyên dương `x` (với `1 <= x <= n`) sẽ nằm ở index `x - 1`. Ta duyệt qua mảng, nếu gặp `nums[i]` thoả mãn điều kiện đó và vị trí đích của nó chưa chứa giá trị đúng, ta tiến hành hoán đổi. Sau đó duyệt một vòng từ đầu, index đầu tiên `i` mà `nums[i] != i + 1` sẽ cho biết `i + 1` chính là số dương nhỏ nhất bị thiếu.
**Mã nguồn Java:**
```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        int i = 0;
        int n = nums.length;
        while (i < n) {
            if (nums[i] > 0 && nums[i] <= n && nums[nums[i] - 1] != nums[i]) {
                int correctIndex = nums[i] - 1;
                int temp = nums[i];
                nums[i] = nums[correctIndex];
                nums[correctIndex] = temp;
            } else {
                i++;
            }
        }
        
        for (int j = 0; j < n; j++) {
            if (nums[j] != j + 1) {
                return j + 1;
            }
        }
        return n + 1; // Nếu đầy đủ từ 1 đến n
    }
}
```
**Độ phức tạp:** Thời gian: O(N). Không gian: O(1).

### 30. H-Index
**Đề bài chi tiết:** Cho một mảng các số nguyên `citations` trong đó `citations[i]` là số lượt trích dẫn bài báo thứ `i` của một nhà nghiên cứu, hãy trả về chỉ số h-index của nhà nghiên cứu đó. H-index là giá trị tối đa `h` sao cho nhà nghiên cứu đó đã xuất bản ít nhất `h` bài báo, mỗi bài báo được trích dẫn ít nhất `h` lần.
**Phân tích thuật toán:** Sắp xếp mảng lượt trích dẫn theo thứ tự tăng dần. Sau đó, duyệt qua mảng từ cuối lên đầu. Với mỗi bài báo, số lượng bài báo có lượt trích dẫn lớn hơn hoặc bằng nó chính là số lượng các phần tử từ vị trí đó đến hết mảng. Ta so sánh giá trị trích dẫn tại vị trí `i` với số lượng bài báo đó, nếu `citations[i] >= n - i`, thì `n - i` có thể là một h-index. Tiếp tục tìm để đạt số `h` lớn nhất.
**Mã nguồn Java:**
```java
class Solution {
    public int hIndex(int[] citations) {
        Arrays.sort(citations);
        int n = citations.length;
        for (int i = 0; i < n; i++) {
            // Số bài báo có trích dẫn >= citations[i]
            int h = n - i; 
            if (citations[i] >= h) {
                return h;
            }
        }
        return 0;
    }
}
```
**Độ phức tạp:** Thời gian: O(N log N) cho việc sắp xếp mảng. Không gian: O(1) hoặc O(log N).
