# Cấu Trúc Dữ Liệu và Thuật Toán: Basic Greedy Problems

Tài liệu này bao gồm 30 bài tập về Thuật toán Tham lam (Greedy). Trong đó, 10 bài đầu tiên sẽ được phân tích chi tiết, giải thích giải thuật và cung cấp mã nguồn Java. 20 bài tiếp theo sẽ được tóm tắt ý tưởng chính.

---

## 1. Assign Cookies (LeetCode 455)
### Đề bài chi tiết
Giả sử bạn là một phụ huynh tuyệt vời và muốn phân phát bánh quy cho các con của mình. Mỗi đứa trẻ $i$ có một độ thèm ăn (greed factor) là $g[i]$. Mỗi chiếc bánh quy $j$ có một kích thước $s[j]$. Nếu $s[j] \ge g[i]$, ta có thể phân chiếc bánh $j$ cho đứa trẻ $i$ và đứa trẻ sẽ hài lòng.
Mục tiêu là làm cho tối đa số lượng trẻ em hài lòng.

### Phân tích thuật toán
- Chúng ta muốn thỏa mãn càng nhiều đứa trẻ càng tốt bằng cách sử dụng những chiếc bánh quy nhỏ nhất có thể.
- **Greedy choice:** Thỏa mãn đứa trẻ có lòng tham nhỏ nhất trước, bằng chiếc bánh quy nhỏ nhất đủ để thỏa mãn nó.
- Sắp xếp cả mảng $g$ và $s$ tăng dần. Dùng hai con trỏ duyệt qua 2 mảng.

### Mã nguồn Java
```java
class Solution {
    public int findContentChildren(int[] g, int[] s) {
        Arrays.sort(g);
        Arrays.sort(s);
        int child = 0;
        int cookie = 0;
        
        while (child < g.length && cookie < s.length) {
            if (s[cookie] >= g[child]) {
                child++; // Đứa trẻ này đã được thỏa mãn
            }
            cookie++; // Dù thỏa mãn hay không, bánh quy này đã được xét
        }
        
        return child;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N \log N + M \log M)$ với $N, M$ là độ dài mảng $g$ và $s$ (do sắp xếp).
- **Không gian bộ nhớ (Space Complexity):** $O(1)$ (hoặc không gian bộ nhớ sắp xếp nội bộ $O(\log N)$ tùy ngôn ngữ).

---

## 2. Fractional Knapsack (GeeksForGeeks)
### Đề bài chi tiết
Cho $N$ đồ vật với trọng lượng (weight) và giá trị (value) tương ứng. Có một cái túi chứa được trọng lượng tối đa là $W$. Khác với bài toán 0/1 Knapsack, bạn có thể cắt nhỏ đồ vật để lấy một phần. Hãy tìm tổng giá trị lớn nhất có thể lấy được.

### Phân tích thuật toán
- **Greedy choice:** Ưu tiên chọn những đồ vật có giá trị trên mỗi đơn vị trọng lượng (value/weight ratio) lớn nhất.
- Tính tỷ lệ cho mỗi đồ vật, sắp xếp giảm dần.
- Duyệt qua danh sách, lấy toàn bộ nếu đồ vật nhét vừa túi. Nếu không vừa, lấy một phần tương ứng với sức chứa còn lại của túi và dừng lại.

### Mã nguồn Java
```java
class Item {
    int value, weight;
    Item(int x, int y) {
        this.value = x;
        this.weight = y;
    }
}

class Solution {
    double fractionalKnapsack(int W, Item arr[], int n) {
        Arrays.sort(arr, (a, b) -> {
            double r1 = (double) a.value / a.weight;
            double r2 = (double) b.value / b.weight;
            return Double.compare(r2, r1); // Giảm dần
        });
        
        double totalValue = 0.0;
        for (int i = 0; i < n; i++) {
            if (W >= arr[i].weight) {
                totalValue += arr[i].value;
                W -= arr[i].weight;
            } else {
                totalValue += arr[i].value * ((double) W / arr[i].weight);
                break;
            }
        }
        return totalValue;
    }
}
```

### Độ phức tạp
- **Thời gian:** $O(N \log N)$ do sorting.
- **Không gian bộ nhớ:** $O(1)$.

---

## 3. Lemonade Change (LeetCode 860)
### Đề bài chi tiết
Tại một quầy bán nước chanh, mỗi ly nước chanh giá $5. Khách hàng đang đứng thành hàng, mỗi người chỉ mua một ly nước và thanh toán bằng tờ $5, $10, hoặc $20. Bạn phải trả lại tiền thối đúng mệnh giá. Bạn khởi đầu không có đồng nào. Trả về true nếu trả được tiền thối cho tất cả, false nếu không.

### Phân tích thuật toán
- Duy trì số lượng tờ $5 và $10 bạn đang có.
- Khách trả $5: Tăng số tờ $5.
- Khách trả $10: Trả lại $5 (nếu có), tăng $10.
- Khách trả $20: **Greedy Choice:** Ưu tiên trả bằng 1 tờ $10 và 1 tờ $5 (vì $5 đa năng hơn trong việc thối lại). Nếu không có tờ $10, mới dùng 3 tờ $5.

### Mã nguồn Java
```java
class Solution {
    public boolean lemonadeChange(int[] bills) {
        int five = 0, ten = 0;
        for (int bill : bills) {
            if (bill == 5) {
                five++;
            } else if (bill == 10) {
                if (five == 0) return false;
                five--;
                ten++;
            } else {
                if (ten > 0 && five > 0) {
                    ten--;
                    five--;
                } else if (five >= 3) {
                    five -= 3;
                } else {
                    return false;
                }
            }
        }
        return true;
    }
}
```

### Độ phức tạp
- **Thời gian:** $O(N)$, duyệt qua mảng 1 lần.
- **Không gian bộ nhớ:** $O(1)$.

---

## 4. Maximum Units on a Truck (LeetCode 1710)
### Đề bài chi tiết
Bạn có một số thùng hàng dạng mảng 2D `boxTypes`, trong đó `boxTypes[i] = [numberOfBoxesi, numberOfUnitsPerBoxi]`. Bạn có một chiếc xe tải với sức chứa là `truckSize` thùng. Hãy tính số đơn vị hàng hóa tối đa (maximum units) có thể chở trên xe tải.

### Phân tích thuật toán
- **Greedy choice:** Lấy các thùng chứa nhiều đơn vị hàng (units) nhất trước để tối đa hóa số lượng units trên một chỗ trống (box) của xe tải.
- Sắp xếp `boxTypes` theo số đơn vị hàng giảm dần.
- Cộng dồn số đơn vị vào xe cho đến khi xe tải đầy.

### Mã nguồn Java
```java
class Solution {
    public int maximumUnits(int[][] boxTypes, int truckSize) {
        // Sắp xếp giảm dần theo số đơn vị (units) của mỗi thùng
        Arrays.sort(boxTypes, (a, b) -> Integer.compare(b[1], a[1]));
        
        int maxUnits = 0;
        for (int[] box : boxTypes) {
            int count = Math.min(box[0], truckSize);
            maxUnits += count * box[1];
            truckSize -= count;
            if (truckSize == 0) break;
        }
        return maxUnits;
    }
}
```

### Độ phức tạp
- **Thời gian:** $O(N \log N)$ (do sắp xếp).
- **Không gian bộ nhớ:** $O(1)$.

---

## 5. Minimum Cost to Move Chips to The Same Position (LeetCode 1217)
### Đề bài chi tiết
Có một số đồng xu (chips), phần tử `position[i]` là vị trí của đồng xu thứ $i$. Chi phí di chuyển đồng xu từ `position[i]` đến `position[i] + 2` hoặc `position[i] - 2` là 0. Di chuyển đến `position[i] + 1` hoặc `position[i] - 1` là 1. Tính tổng chi phí nhỏ nhất để dồn tất cả về cùng một vị trí.

### Phân tích thuật toán
- Bất kỳ đồng xu nào ở vị trí chẵn có thể gom về chung 1 vị trí chẵn với chi phí 0. Tương tự cho vị trí lẻ.
- Tóm lại, ta chỉ cần đếm số đồng xu ở vị trí chẵn và lẻ.
- **Greedy choice:** Di chuyển nhóm nhỏ hơn sang vị trí của nhóm lớn hơn (vì mỗi lần đổi chẵn/lẻ tốn 1 chi phí).

### Mã nguồn Java
```java
class Solution {
    public int minCostToMoveChips(int[] position) {
        int even = 0, odd = 0;
        for (int pos : position) {
            if (pos % 2 == 0) {
                even++;
            } else {
                odd++;
            }
        }
        return Math.min(even, odd);
    }
}
```

### Độ phức tạp
- **Thời gian:** $O(N)$
- **Không gian bộ nhớ:** $O(1)$

---

## 6. Minimum Operations to Make the Array Increasing (LeetCode 1827)
### Đề bài chi tiết
Cho mảng số nguyên `nums`. Một phép toán cho phép bạn tăng một phần tử lên 1. Tìm số phép toán tối thiểu để biến mảng thành mảng tăng ngặt (strictly increasing) - tức là `nums[i] < nums[i+1]`.

### Phân tích thuật toán
- Đi từ trái sang phải, nếu `nums[i]` không lớn hơn `nums[i-1]`, ta buộc phải tăng nó lên sao cho vừa đủ lớn hơn phần tử trước nó (tức là `nums[i-1] + 1`).
- **Greedy choice:** Tăng số hiện tại lên đúng `nums[i-1] + 1` là cách tốt nhất, không tăng thừa vì làm vậy sẽ gây bất lợi cho phần tử tiếp theo.

### Mã nguồn Java
```java
class Solution {
    public int minOperations(int[] nums) {
        int ops = 0;
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] <= nums[i-1]) {
                int increment = nums[i-1] - nums[i] + 1;
                ops += increment;
                nums[i] = nums[i-1] + 1; // Cập nhật mảng
            }
        }
        return ops;
    }
}
```

### Độ phức tạp
- **Thời gian:** $O(N)$
- **Không gian bộ nhớ:** $O(1)$

---

## 7. Array Partition (LeetCode 561)
### Đề bài chi tiết
Cho một mảng có $2N$ số nguyên, hãy chia nó thành $N$ cặp, sao cho tổng các phần tử nhỏ nhất (min) của từng cặp là lớn nhất có thể.

### Phân tích thuật toán
- Lựa chọn min của mỗi cặp có nghĩa là số lớn hơn trong cặp đó sẽ bị lãng phí. Để làm giảm sự lãng phí này, ta nên bắt cặp các số có giá trị gần nhau.
- **Greedy choice:** Sắp xếp mảng, sau đó chọn 2 số liên tiếp làm một cặp. Lấy các giá trị ở chỉ số chẵn.

### Mã nguồn Java
```java
class Solution {
    public int arrayPairSum(int[] nums) {
        Arrays.sort(nums);
        int sum = 0;
        for (int i = 0; i < nums.length; i += 2) {
            sum += nums[i]; // Lấy phần tử nhỏ hơn của mỗi cặp liên tiếp
        }
        return sum;
    }
}
```

### Độ phức tạp
- **Thời gian:** $O(N \log N)$
- **Không gian bộ nhớ:** $O(1)$

---

## 8. Earliest Possible Day of Full Bloom (LeetCode 2136)
### Đề bài chi tiết
Bạn có $N$ hạt giống, với thời gian gieo (plantTime) và thời gian sinh trưởng (growTime). Bạn chỉ gieo được 1 hạt mỗi ngày, nhưng nhiều hạt có thể cùng phát triển một lúc. Tìm số ngày ít nhất để tất cả các hoa nở.

### Phân tích thuật toán
- Thời gian plantTime luôn phải dùng hết cho dù thứ tự nào. Điều duy nhất cần tối ưu là giảm thời gian chờ ở cuối do growTime.
- **Greedy choice:** Ưu tiên gieo trồng những hạt có thời gian phát triển (`growTime`) lâu nhất trước, để chúng kịp lớn trong lúc ta đang trồng những hạt khác.

### Mã nguồn Java
```java
class Solution {
    public int earliestFullBloom(int[] plantTime, int[] growTime) {
        int n = plantTime.length;
        Integer[] indices = new Integer[n];
        for (int i = 0; i < n; i++) indices[i] = i;
        
        // Sắp xếp giảm dần theo growTime
        Arrays.sort(indices, (a, b) -> Integer.compare(growTime[b], growTime[a]));
        
        int currentPlantTime = 0;
        int maxTime = 0;
        
        for (int i = 0; i < n; i++) {
            int idx = indices[i];
            currentPlantTime += plantTime[idx];
            maxTime = Math.max(maxTime, currentPlantTime + growTime[idx]);
        }
        
        return maxTime;
    }
}
```

### Độ phức tạp
- **Thời gian:** $O(N \log N)$
- **Không gian bộ nhớ:** $O(N)$ (lưu mảng chỉ số indices).

---

## 9. Maximum 69 Number (LeetCode 1323)
### Đề bài chi tiết
Cho một số thập phân chỉ bao gồm các chữ số '6' và '9'. Trả về số lớn nhất có thể nhận được bằng cách đổi tối đa một chữ số (6 thành 9, hoặc 9 thành 6).

### Phân tích thuật toán
- Đổi 9 thành 6 luôn làm giảm giá trị của số $\rightarrow$ ta chỉ nên đổi 6 thành 9.
- Đổi 6 thành 9 ở hàng phần nghìn tốt hơn hàng đơn vị $\rightarrow$ **Greedy choice:** Đổi số 6 đầu tiên (từ trái qua phải) thành 9.

### Mã nguồn Java
```java
class Solution {
    public int maximum69Number (int num) {
        char[] chars = String.valueOf(num).toCharArray();
        for (int i = 0; i < chars.length; i++) {
            if (chars[i] == '6') {
                chars[i] = '9';
                break;
            }
        }
        return Integer.parseInt(new String(chars));
    }
}
```

### Độ phức tạp
- **Thời gian:** $O(K)$ với $K$ là số chữ số (khoảng max 4 theo điều kiện đề bài). Tương đương $O(1)$.
- **Không gian bộ nhớ:** $O(K) \approx O(1)$.

---

## 10. Valid Palindrome II (LeetCode 680)
### Đề bài chi tiết
Cho chuỗi $s$, trả về true nếu chuỗi có thể biến thành chuỗi đối xứng (palindrome) bằng cách xóa tối đa MỘT ký tự.

### Phân tích thuật toán
- Sử dụng hai con trỏ, một ở đầu, một ở cuối đi vào trong.
- Nếu thấy 2 ký tự khác nhau ở vị trí $i$ và $j$, ta bắt buộc phải xóa một trong hai.
- **Greedy choice:** Ta không biết xóa cái nào tốt hơn, nên ta thử kiểm tra 2 nhánh con: chuỗi con `s[i+1...j]` và `s[i...j-1]`. Chỉ cần 1 trong 2 chuỗi là palindrome, trả về true.

### Mã nguồn Java
```java
class Solution {
    public boolean validPalindrome(String s) {
        int i = 0, j = s.length() - 1;
        while (i < j) {
            if (s.charAt(i) != s.charAt(j)) {
                return isPalindrome(s, i + 1, j) || isPalindrome(s, i, j - 1);
            }
            i++;
            j--;
        }
        return true;
    }
    
    private boolean isPalindrome(String s, int i, int j) {
        while (i < j) {
            if (s.charAt(i) != s.charAt(j)) return false;
            i++;
            j--;
        }
        return true;
    }
}
```

### Độ phức tạp
- **Thời gian:** $O(N)$
- **Không gian bộ nhớ:** $O(1)$

---

## 11. Split a String in Balanced Strings (LeetCode 1221)
### Đề bài chi tiết
Cho một chuỗi `s` chỉ chứa hai ký tự 'L' và 'R'. Chuỗi cân bằng là chuỗi có số lượng 'L' và 'R' bằng nhau. Trả về số lượng chuỗi con cân bằng tối đa có thể cắt ra từ chuỗi gốc `s`.

### Phân tích thuật toán
- Khởi tạo một biến đếm `balance` và `count`. Duyệt qua chuỗi `s`, gặp 'L' thì cộng 1, gặp 'R' thì trừ 1.
- Bất cứ khi nào `balance` bằng 0, nghĩa là ta có một chuỗi con cân bằng, tăng `count` lên 1.
- **Greedy choice:** Việc cắt ngay lập tức khi cân bằng sẽ tối đa hóa số lượng chuỗi được cắt, vì mọi chuỗi cân bằng lớn hơn đều có thể chia nhỏ thành các chuỗi con cân bằng.

### Mã nguồn Java
```java
class Solution {
    public int balancedStringSplit(String s) {
        int balance = 0;
        int count = 0;
        for (char c : s.toCharArray()) {
            if (c == 'L') {
                balance++;
            } else {
                balance--;
            }
            
            if (balance == 0) {
                count++;
            }
        }
        return count;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N)$, với $N$ là độ dài chuỗi $s$.
- **Không gian bộ nhớ (Space Complexity):** $O(1)$.

---

## 12. DI String Match (LeetCode 942)
### Đề bài chi tiết
Cho một chuỗi `s` độ dài $N$ chỉ chứa các ký tự 'I' (Increase) và 'D' (Decrease). Hãy tạo một mảng hoán vị `perm` của các số nguyên từ $0$ đến $N$ sao cho:
- Nếu `s[i] == 'I'`, thì `perm[i] < perm[i+1]`
- Nếu `s[i] == 'D'`, thì `perm[i] > perm[i+1]`

### Phân tích thuật toán
- Sử dụng hai con trỏ `low = 0` và `high = N`.
- **Greedy choice:** Khi gặp ký tự 'I' (cần số tiếp theo lớn hơn), ta gán cho phần tử hiện tại giá trị nhỏ nhất có thể (`low`) và tăng `low` lên 1. Ngược lại, khi gặp 'D' (cần số tiếp theo nhỏ hơn), ta gán giá trị lớn nhất có thể (`high`) và giảm `high` đi 1.
- Cuối cùng, gán phần tử duy nhất còn lại cho vị trí cuối.

### Mã nguồn Java
```java
class Solution {
    public int[] diStringMatch(String s) {
        int n = s.length();
        int[] perm = new int[n + 1];
        int low = 0, high = n;
        
        for (int i = 0; i < n; i++) {
            if (s.charAt(i) == 'I') {
                perm[i] = low++;
            } else {
                perm[i] = high--;
            }
        }
        perm[n] = low; // Phần tử cuối cùng
        return perm;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N)$
- **Không gian bộ nhớ (Space Complexity):** $O(N)$ (để lưu mảng kết quả).

---

## 13. Delete Columns to Make Sorted (LeetCode 944)
### Đề bài chi tiết
Cho một mảng `strs` chứa $N$ chuỗi có cùng độ dài. Có thể coi các chuỗi được sắp xếp thành một lưới (mỗi chuỗi là một hàng). Bạn có quyền xóa đi bất kỳ cột nào. Trả về số lượng cột tối thiểu cần xóa sao cho mọi cột còn lại đều được sắp xếp theo thứ tự tăng dần từ trên xuống dưới.

### Phân tích thuật toán
- **Greedy choice:** Việc các cột khác được giữ lại hay xóa đi hoàn toàn không ảnh hưởng đến tính hợp lệ của cột hiện tại. Ta kiểm tra độc lập từng cột.
- Nếu cột hiện tại không được sắp xếp theo thứ tự tăng dần từ trên xuống dưới, ta bắt buộc phải xóa nó, cộng vào biến đếm số cột cần xóa.

### Mã nguồn Java
```java
class Solution {
    public int minDeletionSize(String[] strs) {
        int deleteCount = 0;
        int rowCount = strs.length;
        int colCount = strs[0].length();
        
        for (int col = 0; col < colCount; col++) {
            for (int row = 1; row < rowCount; row++) {
                // Nếu phát hiện phần tử dưới nhỏ hơn phần tử trên -> Xóa cột
                if (strs[row].charAt(col) < strs[row - 1].charAt(col)) {
                    deleteCount++;
                    break;
                }
            }
        }
        
        return deleteCount;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N \times M)$ với $N$ là số hàng, $M$ là số cột.
- **Không gian bộ nhớ (Space Complexity):** $O(1)$.

---

## 14. Minimum Subsequence in Non-Increasing Order (LeetCode 1403)
### Đề bài chi tiết
Cho mảng số nguyên `nums`. Trả về một chuỗi con (subsequence) của mảng sao cho tổng các phần tử của nó lớn hơn tổng các phần tử còn lại của mảng. Chuỗi con được chọn phải có số phần tử ít nhất, trong trường hợp có nhiều đáp án thì chọn chuỗi có tổng lớn nhất. Kết quả trả về phải sắp xếp theo thứ tự giảm dần.

### Phân tích thuật toán
- **Greedy choice:** Để lấy số lượng phần tử ít nhất nhưng nhanh chóng đạt được tổng lớn, ta ưu tiên lấy các phần tử có giá trị lớn nhất trong mảng.
- Thuật toán: Tính tổng toàn bộ mảng. Sắp xếp mảng tăng dần, sau đó duyệt ngược từ cuối về đầu, lấy dần các phần tử vào kết quả và cập nhật tổng. Dừng lại khi tổng các số đã lấy lớn hơn một nửa tổng toàn mảng.

### Mã nguồn Java
```java
class Solution {
    public List<Integer> minSubsequence(int[] nums) {
        Arrays.sort(nums);
        int totalSum = 0;
        for (int num : nums) {
            totalSum += num;
        }
        
        List<Integer> result = new ArrayList<>();
        int currentSum = 0;
        
        for (int i = nums.length - 1; i >= 0; i--) {
            currentSum += nums[i];
            result.add(nums[i]);
            if (currentSum > totalSum - currentSum) {
                break;
            }
        }
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N \log N)$ (do thao tác sắp xếp).
- **Không gian bộ nhớ (Space Complexity):** $O(1)$ (nếu không tính danh sách trả về).

---

## 15. Can Place Flowers (LeetCode 605)
### Đề bài chi tiết
Bạn có một luống hoa dạng mảng số nguyên `flowerbed` gồm các số 0 (trống) và 1 (đã trồng hoa). Hoa không thể được trồng ở hai ô kề nhau. Cho số nguyên `n`, kiểm tra xem có thể trồng thêm `n` bông hoa vào luống mà không vi phạm quy tắc không.

### Phân tích thuật toán
- **Greedy choice:** Duyệt từ trái sang phải, cứ khi nào thấy một vị trí hợp lệ (ô hiện tại trống, và không bị kề với hoa nào ở bên trái hay phải), ta lập tức trồng ngay một bông hoa vào đó. Việc trồng hoa sớm nhất có thể luôn mở ra thêm nhiều không gian trống cho các cây phía sau so với việc để trống ô đó.

### Mã nguồn Java
```java
class Solution {
    public boolean canPlaceFlowers(int[] flowerbed, int n) {
        int count = 0;
        for (int i = 0; i < flowerbed.length; i++) {
            if (flowerbed[i] == 0) {
                boolean emptyLeft = (i == 0 || flowerbed[i - 1] == 0);
                boolean emptyRight = (i == flowerbed.length - 1 || flowerbed[i + 1] == 0);
                
                if (emptyLeft && emptyRight) {
                    flowerbed[i] = 1;
                    count++;
                    if (count >= n) {
                        return true;
                    }
                }
            }
        }
        return count >= n;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N)$, với $N$ là kích thước mảng.
- **Không gian bộ nhớ (Space Complexity):** $O(1)$.

---

## 16. Task Scheduler (LeetCode 621)
### Đề bài chi tiết
Cho một mảng `tasks` đại diện cho các công việc CPU cần thực thi, mỗi công việc ký hiệu bằng một chữ cái in hoa. Có tham số `n` là thời gian làm mát (cooldown): hai công việc giống nhau phải cách nhau tối thiểu `n` đơn vị thời gian. Tìm tổng thời gian tối thiểu để hoàn thành tất cả công việc.

### Phân tích thuật toán
- Các công việc xuất hiện nhiều lần nhất chính là "nút thắt" quyết định thời gian tối thiểu.
- **Greedy choice:** Ta phân bổ các công việc có tần suất lớn nhất trước để tạo ra các "khoảng trống" (idle slots), rồi điền dần các công việc khác vào các khoảng trống này.
- Số khoảng trống tối đa tạo ra bởi công việc xuất hiện `maxFreq` lần là `(maxFreq - 1) * (n - (số công việc cũng đạt maxFreq - 1))`. 
- Tổng thời gian bằng số công việc cộng với thời gian chờ (idle time) nếu có. 

### Mã nguồn Java
```java
class Solution {
    public int leastInterval(char[] tasks, int n) {
        int[] freq = new int[26];
        for (char task : tasks) {
            freq[task - 'A']++;
        }
        
        Arrays.sort(freq);
        int maxFreq = freq[25];
        
        int maxCount = 0;
        for (int i = 25; i >= 0; i--) {
            if (freq[i] == maxFreq) {
                maxCount++;
            } else {
                break;
            }
        }
        
        int emptySlots = (maxFreq - 1) * (n - (maxCount - 1));
        int availableTasks = tasks.length - maxFreq * maxCount;
        int idles = Math.max(0, emptySlots - availableTasks);
        
        return tasks.length + idles;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N)$ để đếm tần số, sắp xếp mảng 26 phần tử mất $O(1)$. Tổng cộng $O(N)$.
- **Không gian bộ nhớ (Space Complexity):** $O(1)$ (mảng cố định 26 phần tử).

---

## 17. Maximize Sum Of Array After K Negations (LeetCode 1005)
### Đề bài chi tiết
Cho mảng số nguyên `nums` và một số nguyên `k`. Bạn phải thực hiện chính xác `k` thao tác: chọn một phần tử `nums[i]` và đổi dấu nó thành `-nums[i]`. Tìm tổng lớn nhất có thể của mảng sau khi hoàn thành `k` phép toán.

### Phân tích thuật toán
- **Greedy choice:** Để tối đa hóa tổng, ta ưu tiên biến đổi các số âm thành số dương. Trong đó, số âm có giá trị tuyệt đối lớn nhất phải được biến đổi trước.
- Thuật toán: 
  1. Sắp xếp mảng để các số âm nhỏ nhất (giá trị tuyệt đối lớn nhất) nằm ở đầu.
  2. Lần lượt đổi dấu số âm, giảm dần $k$.
  3. Nếu $k$ vẫn còn mà hết số âm, ta kiểm tra xem $k$ là chẵn hay lẻ. Nếu chẵn, ta đổi một số chẵn lần không thay đổi tổng. Nếu lẻ, ta đành hy sinh (đổi dấu thành âm) phần tử có giá trị tuyệt đối nhỏ nhất.

### Mã nguồn Java
```java
class Solution {
    public int largestSumAfterKNegations(int[] nums, int k) {
        Arrays.sort(nums);
        for (int i = 0; i < nums.length && k > 0; i++) {
            if (nums[i] < 0) {
                nums[i] = -nums[i];
                k--;
            }
        }
        
        // Tìm phần tử nhỏ nhất sau khi đã lật hết số âm
        int min = Integer.MAX_VALUE;
        int sum = 0;
        for (int num : nums) {
            if (num < min) min = num;
            sum += num;
        }
        
        // Nếu k lẻ, lật phần tử nhỏ nhất 1 lần (tức là trừ đi 2 lần giá trị của nó)
        if (k % 2 == 1) {
            sum -= 2 * min;
        }
        
        return sum;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N \log N)$ (do bước sắp xếp mảng).
- **Không gian bộ nhớ (Space Complexity):** $O(1)$.

---

## 18. Two City Scheduling (LeetCode 1029)
### Đề bài chi tiết
Có $2N$ người cần phỏng vấn tại hai thành phố A và B. Chi phí bay người thứ $i$ đến A là `costs[i][0]` và đến B là `costs[i][1]`. Bạn cần đưa đúng $N$ người đến thành phố A và $N$ người đến thành phố B. Tính chi phí tối thiểu để thực hiện.

### Phân tích thuật toán
- **Greedy choice:** Giả sử tất cả mọi người đều bay đến thành phố A. Chi phí tổng sẽ là $\sum costs[i][0]$. Giờ ta phải chọn $N$ người đổi sang bay đến B. Khi đổi, chi phí thay đổi một khoảng là `costs[i][1] - costs[i][0]`.
- Ta nên đổi $N$ người mang lại nhiều tiền "tiết kiệm" nhất, tức là người có độ chênh lệch `costs[i][1] - costs[i][0]` là nhỏ nhất (âm càng nhiều càng tốt). Ta sắp xếp mảng theo hiệu số này và gửi $N$ người đầu tiên đến B, $N$ người còn lại đến A.

### Mã nguồn Java
```java
class Solution {
    public int twoCitySchedCost(int[][] costs) {
        // Sắp xếp theo hiệu số chi phí: (Tới B - Tới A)
        Arrays.sort(costs, (a, b) -> Integer.compare(a[1] - a[0], b[1] - b[0]));
        
        int totalCost = 0;
        int n = costs.length / 2;
        
        // N người đầu tiên (mang lại tiết kiệm lớn nhất) sẽ đến B
        for (int i = 0; i < n; i++) {
            totalCost += costs[i][1];
        }
        
        // N người còn lại sẽ đến A
        for (int i = n; i < costs.length; i++) {
            totalCost += costs[i][0];
        }
        
        return totalCost;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N \log N)$ (sắp xếp chi phí của $2N$ người).
- **Không gian bộ nhớ (Space Complexity):** $O(1)$.

---

## 19. Partition Labels (LeetCode 763)
### Đề bài chi tiết
Cho chuỗi `s` gồm các chữ cái viết thường. Ta cần chia chuỗi thành nhiều phần (partitions) nhất có thể, sao cho mỗi ký tự chỉ xuất hiện trong tối đa một phần. Trả về danh sách chứa độ dài của các phần.

### Phân tích thuật toán
- **Greedy choice:** Để đảm bảo một ký tự chỉ xuất hiện ở một phân đoạn, phân đoạn đó phải bao trọn khoảng từ vị trí đầu đến vị trí cuối cùng của ký tự đó.
- Thuật toán: Duyệt chuỗi lần 1 để lưu lại chỉ số (index) xuất hiện cuối cùng của từng ký tự. Duyệt chuỗi lần 2, liên tục cập nhật vùng bị bao phủ lớn nhất `end = max(end, lastIndex[char])`. Khi duyệt đến `i == end`, ta xác nhận có thể đóng được phân đoạn hiện tại và tạo phân đoạn mới.

### Mã nguồn Java
```java
class Solution {
    public List<Integer> partitionLabels(String s) {
        int[] lastIndices = new int[26];
        for (int i = 0; i < s.length(); i++) {
            lastIndices[s.charAt(i) - 'a'] = i;
        }
        
        List<Integer> result = new ArrayList<>();
        int start = 0;
        int end = 0;
        for (int i = 0; i < s.length(); i++) {
            end = Math.max(end, lastIndices[s.charAt(i) - 'a']);
            if (i == end) {
                result.add(end - start + 1);
                start = i + 1; // Khởi đầu mới cho phân đoạn tiếp theo
            }
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N)$, với $N$ là độ dài chuỗi $s$ (duyệt 2 lần).
- **Không gian bộ nhớ (Space Complexity):** $O(1)$ (mảng 26 ký tự).

---

## 20. Jump Game (LeetCode 55)
### Đề bài chi tiết
Cho mảng số nguyên không âm `nums`. Ban đầu bạn đứng ở vị trí đầu tiên (chỉ số 0), mỗi phần tử `nums[i]` đại diện cho số bước tối đa bạn có thể nhảy từ vị trí đó. Hãy kiểm tra xem bạn có thể nhảy đến phần tử cuối cùng của mảng hay không.

### Phân tích thuật toán
- Ta dùng một biến `farthest` để lưu lại vị trí xa nhất mà ta có thể nhảy tới tính từ những vị trí đã xét.
- **Greedy choice:** Ở mỗi bước $i$, khoảng cách lớn nhất có thể vươn tới là $i + nums[i]$. Ta liên tục cập nhật `farthest = max(farthest, i + nums[i])`. 
- Nếu đến một vị trí $i$ nào đó mà $i > farthest$, nghĩa là ta đã bị kẹt và không bao giờ vượt qua được $i$, trả về `false`. Nếu `farthest` vượt qua điểm đích, trả về `true`.

### Mã nguồn Java
```java
class Solution {
    public boolean canJump(int[] nums) {
        int farthest = 0;
        for (int i = 0; i < nums.length; i++) {
            // Nếu vị trí hiện tại nằm ngoài tầm nhảy xa nhất, không thể đi tiếp
            if (i > farthest) {
                return false;
            }
            farthest = Math.max(farthest, i + nums[i]);
            // Nếu đã với tới đích
            if (farthest >= nums.length - 1) {
                return true;
            }
        }
        return true;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N)$, với $N$ là kích thước mảng.
- **Không gian bộ nhớ (Space Complexity):** $O(1)$.

---

## 21. Gas Station (LeetCode 134)
### Đề bài chi tiết
Có $N$ trạm xăng xếp thành vòng tròn, lượng xăng tại trạm $i$ là `gas[i]`. Bạn có một chiếc xe với bình xăng vô hạn, chi phí xăng để đi từ trạm $i$ tới trạm $i+1$ là `cost[i]`. Bạn bắt đầu tại một trạm xăng bất kỳ với bình xăng rỗng. Trả về chỉ số trạm xuất phát để bạn có thể đi một vòng quanh tất cả các trạm, hoặc trả về -1 nếu không thể. (Đảm bảo có nghiệm duy nhất nếu có).

### Phân tích thuật toán
- Nếu tổng lượng xăng (`sum(gas)`) nhỏ hơn tổng chi phí (`sum(cost)`), chắc chắn không thể đi đủ một vòng. Ngược lại, chắn chắn sẽ có một nghiệm duy nhất (theo đề bài).
- Khởi tạo `totalTank = 0`, `currTank = 0`, `start = 0`. Duyệt qua các trạm, cộng dồn nhiên liệu tại mỗi bước `currTank += gas[i] - cost[i]`.
- **Greedy choice:** Nếu `currTank < 0` tại trạm $i$, điều đó có nghĩa là xe không thể đi từ bất cứ trạm nào từ điểm `start` hiện tại cho đến trạm $i$. Do đó, trạm tiềm năng để xuất phát tiếp theo phải là $i+1$. Ta reset `currTank = 0` và đặt `start = i + 1`.

### Mã nguồn Java
```java
class Solution {
    public int canCompleteCircuit(int[] gas, int[] cost) {
        int totalTank = 0;
        int currTank = 0;
        int start = 0;
        
        for (int i = 0; i < gas.length; i++) {
            totalTank += gas[i] - cost[i];
            currTank += gas[i] - cost[i];
            
            // Nếu không thể đến trạm tiếp theo, ta làm lại từ trạm i+1
            if (currTank < 0) {
                start = i + 1;
                currTank = 0;
            }
        }
        
        return totalTank >= 0 ? start : -1;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N)$, duyệt qua mảng đúng một vòng.
- **Không gian bộ nhớ (Space Complexity):** $O(1)$.

---

## 22. Candy (LeetCode 135)
### Đề bài chi tiết
Có $N$ đứa trẻ xếp hàng, mỗi đứa có một điểm đánh giá (`rating`). Bạn phải phân phát kẹo sao cho mỗi đứa trẻ nhận ít nhất 1 viên kẹo. Đứa trẻ có rating cao hơn đứa trẻ đứng cạnh nó phải nhận được nhiều kẹo hơn. Tính tổng số viên kẹo tối thiểu cần phân phát.

### Phân tích thuật toán
- **Greedy choice:** Việc phải lớn hơn hàng xóm bên trái và hàng xóm bên phải có thể được giải quyết theo 2 hướng độc lập.
- Bước 1: Quét từ trái sang phải. Khởi tạo mảng kẹo với 1 viên cho tất cả. Nếu `ratings[i] > ratings[i-1]`, ta cấp `candies[i] = candies[i-1] + 1` (đảm bảo luật với hàng xóm bên trái).
- Bước 2: Quét từ phải sang trái. Nếu `ratings[i] > ratings[i+1]`, ta cập nhật `candies[i] = max(candies[i], candies[i+1] + 1)`. Phép `max` giúp ta giữ vững được luật đã áp dụng với hàng xóm bên trái ở Bước 1.

### Mã nguồn Java
```java
class Solution {
    public int candy(int[] ratings) {
        int n = ratings.length;
        int[] candies = new int[n];
        Arrays.fill(candies, 1);
        
        // Quét trái sang phải
        for (int i = 1; i < n; i++) {
            if (ratings[i] > ratings[i - 1]) {
                candies[i] = candies[i - 1] + 1;
            }
        }
        
        // Quét phải sang trái và cập nhật tổng luôn
        int total = candies[n - 1];
        for (int i = n - 2; i >= 0; i--) {
            if (ratings[i] > ratings[i + 1]) {
                candies[i] = Math.max(candies[i], candies[i + 1] + 1);
            }
            total += candies[i];
        }
        
        return total;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N)$, duyệt 2 vòng lặp độc lập.
- **Không gian bộ nhớ (Space Complexity):** $O(N)$ (cho mảng kẹo).

---

## 23. Wiggle Subsequence (LeetCode 376)
### Đề bài chi tiết
Một chuỗi "wiggle" (ngoằn ngoèo) là chuỗi có hiệu giữa các phần tử liên tiếp luân phiên nhau giữa số dương và âm. (Mảng 1 phần tử hoặc 2 phần tử khác nhau cũng được tính). Tìm độ dài chuỗi con (subsequence) dài nhất thỏa mãn điều kiện wiggle từ một mảng số nguyên.

### Phân tích thuật toán
- Xem mảng như một biểu đồ tuyến tính, ta chỉ muốn nhặt lấy những điểm là **đỉnh** (peak) hoặc **đáy** (valley). Nếu đang lên dốc hoặc xuống dốc liên tục, ta bỏ qua tất cả điểm giữa và chỉ giữ điểm cuối cùng để có cơ hội đảo chiều lớn nhất.
- **Greedy choice:** 
  - Lưu trạng thái: `up` là độ dài wiggle lớn nhất kết thúc bằng một khoảng tăng; `down` là độ dài lớn nhất kết thúc bằng một khoảng giảm.
  - Khi gặp giá trị tăng `nums[i] > nums[i-1]`, ta lấy `up = down + 1`.
  - Khi gặp giá trị giảm `nums[i] < nums[i-1]`, ta lấy `down = up + 1`.

### Mã nguồn Java
```java
class Solution {
    public int wiggleMaxLength(int[] nums) {
        if (nums.length < 2) return nums.length;
        
        int up = 1, down = 1;
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] > nums[i - 1]) {
                up = down + 1;
            } else if (nums[i] < nums[i - 1]) {
                down = up + 1;
            }
        }
        
        return Math.max(up, down);
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N)$, chỉ cần 1 vòng duyệt.
- **Không gian bộ nhớ (Space Complexity):** $O(1)$.

---

## 24. Jump Game II (LeetCode 45)
### Đề bài chi tiết
Cho mảng `nums`, `nums[i]` đại diện độ dài số bước lớn nhất có thể nhảy từ vị trí $i$. Khẳng định rằng bạn luôn có thể nhảy tới vị trí đích (vị trí cuối mảng). Tìm số bước tối thiểu để nhảy tới đích.

### Phân tích thuật toán
- Ta không vội đếm số bước mỗi khi thử nhảy, mà chỉ đếm khi đã chạm giới hạn của lần nhảy trước.
- **Greedy choice:** Tại mỗi lần nhảy hiện tại, ta có một tầm với giới hạn (`currentEnd`). Trong tầm giới hạn đó, ta kiểm tra hết tất cả các ô xem ô nào cung cấp sức nhảy vươn xa nhất trong tương lai (`farthest = max(farthest, i + nums[i])`).
- Khi ta chạm tới `currentEnd`, đó là lúc ta bắt buộc phải tốn thêm 1 bước nhảy để chuyển sang ranh giới mới là `farthest`.

### Mã nguồn Java
```java
class Solution {
    public int jump(int[] nums) {
        int jumps = 0, currentEnd = 0, farthest = 0;
        
        for (int i = 0; i < nums.length - 1; i++) {
            // Tìm khoảng xa nhất có thể đạt được tính từ mọi vị trí trong ranh giới hiện tại
            farthest = Math.max(farthest, i + nums[i]);
            
            // Khi tới cuối ranh giới của bước nhảy hiện tại, ta phải tiến hành nhảy bước mới
            if (i == currentEnd) {
                jumps++;
                currentEnd = farthest;
            }
        }
        return jumps;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N)$.
- **Không gian bộ nhớ (Space Complexity):** $O(1)$.

---

## 25. Bag of Tokens (LeetCode 948)
### Đề bài chi tiết
Bạn có một chỉ số năng lượng `power`, một điểm `score` khởi đầu là 0, và một mảng `tokens`. Bạn có thể chơi token theo hai cách:
- Lật ngửa (face-up): Mất `tokens[i]` năng lượng, tăng 1 điểm score. Yêu cầu `power >= tokens[i]`.
- Lật sấp (face-down): Giảm 1 điểm score, nhận lại `tokens[i]` năng lượng. Yêu cầu `score >= 1`.
Mỗi token chỉ được chơi một lần. Hãy tìm điểm score lớn nhất có thể.

### Phân tích thuật toán
- **Greedy choice:** Để tối đa hóa score, nếu đổi năng lượng lấy điểm (face-up), ta phải trả ít năng lượng nhất $\rightarrow$ dùng token bé nhất. Còn nếu buộc phải "bán" điểm lấy năng lượng (face-down), ta phải kiếm được nhiều năng lượng nhất $\rightarrow$ bán ở token lớn nhất.
- Sắp xếp token tăng dần. Sử dụng con trỏ `left` và `right`. Nếu dư dả `power`, tiêu vào `left` lấy điểm. Nếu cạn kiệt năng lượng, dùng điểm lấy năng lượng từ `right` (chỉ khi nó còn giúp mua thêm được token khác).

### Mã nguồn Java
```java
class Solution {
    public int bagOfTokensScore(int[] tokens, int power) {
        Arrays.sort(tokens);
        int left = 0, right = tokens.length - 1;
        int score = 0;
        int maxScore = 0;
        
        while (left <= right) {
            if (power >= tokens[left]) {
                power -= tokens[left];
                score++;
                maxScore = Math.max(maxScore, score); // Cập nhật maxScore
                left++;
            } else if (score > 0) {
                power += tokens[right]; // Hy sinh 1 score để lấy thêm power
                score--;
                right--;
            } else {
                break; // Không có power và cũng không có score
            }
        }
        
        return maxScore;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N \log N)$ do sắp xếp mảng.
- **Không gian bộ nhớ (Space Complexity):** $O(1)$.

---

## 26. Advantage Shuffle (LeetCode 870)
### Đề bài chi tiết
Cho hai mảng `nums1` và `nums2` cùng kích thước. Điểm lợi thế của `nums1` đối với `nums2` là số vị trí có `nums1[i] > nums2[i]`. Sắp xếp lại `nums1` để điểm lợi thế là cao nhất.

### Phân tích thuật toán
- Đây là bài toán tương tự cách đua ngựa của Điền Kỵ. Ngựa giỏi nhất của ta đấu với ngựa giỏi nhất của địch. Nếu ta khỏe hơn, ta lấy điểm. Nếu ta không thắng nổi ngựa giỏi nhất của địch, ta hy sinh con ngựa kém nhất của mình để "chịu thua".
- Sắp xếp `nums1` tăng dần. Dùng một Max-Heap (hoặc mảng chứa index) sắp xếp mảng `nums2` để ưu tiên phần tử lớn nhất.
- **Greedy choice:** So sánh phần tử lớn nhất của `nums1` với lớn nhất của `nums2`. Nếu thắng được, đưa vào đúng vị trí. Nếu không thắng, nhét phần tử nhỏ nhất của `nums1` vào đó.

### Mã nguồn Java
```java
class Solution {
    public int[] advantageCount(int[] nums1, int[] nums2) {
        Arrays.sort(nums1);
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> b[0] - a[0]); // Max-Heap
        for (int i = 0; i < nums2.length; i++) {
            pq.offer(new int[]{nums2[i], i});
        }
        
        int[] result = new int[nums1.length];
        int left = 0, right = nums1.length - 1;
        
        while (!pq.isEmpty()) {
            int[] current = pq.poll();
            int val = current[0], idx = current[1];
            
            if (nums1[right] > val) {
                // Đủ sức thắng ngựa địch, dùng ngựa xịn nhất của mình
                result[idx] = nums1[right];
                right--;
            } else {
                // Không đủ sức thắng, dùng con ngựa phế nhất
                result[idx] = nums1[left];
                left++;
            }
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N \log N)$ (do sắp xếp mảng và thao tác PriorityQueue).
- **Không gian bộ nhớ (Space Complexity):** $O(N)$ (cho PriorityQueue và mảng kết quả).

---

## 27. Maximum Length of Pair Chain (LeetCode 646)
### Đề bài chi tiết
Bạn có tập các cặp số `pairs`, nơi `pairs[i] = [left_i, right_i]` với `left_i < right_i`. Một chuỗi cặp số có độ dài k được định nghĩa khi `right_i < left_i+1` (điểm kết thúc phần tử trước phải nhỏ hơn điểm bắt đầu phần tử sau). Tìm chuỗi dài nhất có thể được hình thành.

### Phân tích thuật toán
- **Greedy choice:** Tương tự bài toán Interval Scheduling (Chọn lịch làm việc). Một cặp có thời gian kết thúc (điểm `right`) càng sớm sẽ để dành lại nhiều không gian cho các cặp khác tiếp nối sau nó.
- Ta sắp xếp mảng tăng dần theo giá trị `right`. Ta lưu lại điểm kết thúc gần nhất `currentEnd`. Nếu một khoảng có `left > currentEnd`, ta nhận nó vào dây truyền và cập nhật lại `currentEnd = right`.

### Mã nguồn Java
```java
class Solution {
    public int findLongestChain(int[][] pairs) {
        // Sắp xếp theo giá trị right (kết thúc sớm nhất)
        Arrays.sort(pairs, (a, b) -> Integer.compare(a[1], b[1]));
        
        int currentEnd = Integer.MIN_VALUE;
        int count = 0;
        
        for (int[] pair : pairs) {
            if (pair[0] > currentEnd) {
                count++;
                currentEnd = pair[1]; // Cập nhật mốc giới hạn mới
            }
        }
        
        return count;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N \log N)$, với $N$ là số lượng cặp.
- **Không gian bộ nhớ (Space Complexity):** $O(1)$.

---

## 28. Minimum Number of Arrows to Burst Balloons (LeetCode 452)
### Đề bài chi tiết
Có một số bóng bay hình cầu ở không gian tọa độ được biểu diễn bằng các khoảng `[x_start, x_end]`. Ta bắn mũi tên thẳng lên dọc theo trục Y, quả bóng sẽ bị nổ nếu `x_start <= x_arrow <= x_end`. Tìm số mũi tên tối thiểu cần bắn để nổ hết bóng.

### Phân tích thuật toán
- **Greedy choice:** Tương tự như bài toán các khoảng lồng nhau. Để nổ được nhiều bóng nhất với chỉ 1 mũi tên, ta nên bắn mũi tên vào điểm kết thúc (`x_end`) của quả bóng kết thúc sớm nhất. Bất kỳ quả bóng nào đè vào vị trí này cũng sẽ nổ cùng.
- Khi gặp một quả bóng nằm hoàn toàn ngoài tầm mũi tên hiện tại (tức là `x_start > currentArrowPos`), ta buộc phải bắn một mũi tên mới vào điểm kết thúc của quả bóng đó.

### Mã nguồn Java
```java
class Solution {
    public int findMinArrowShots(int[][] points) {
        if (points.length == 0) return 0;
        
        // Sắp xếp theo giá trị x_end
        Arrays.sort(points, (a, b) -> Integer.compare(a[1], b[1]));
        
        int arrows = 1;
        int currentArrowPos = points[0][1];
        
        for (int i = 1; i < points.length; i++) {
            // Bóng tiếp theo hoàn toàn lệch khỏi vị trí tên hiện tại
            if (points[i][0] > currentArrowPos) {
                arrows++;
                currentArrowPos = points[i][1];
            }
        }
        
        return arrows;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N \log N)$ (do sắp xếp).
- **Không gian bộ nhớ (Space Complexity):** $O(1)$.

---

## 29. Non-overlapping Intervals (LeetCode 435)
### Đề bài chi tiết
Cho tập hợp các khoảng `intervals`, tìm số lượng tối thiểu các khoảng cần xóa để các khoảng còn lại hoàn toàn không bị đè lên nhau (chồng chéo nhau). Các khoảng có điểm kết nối sát nhau (`[1,2]` và `[2,3]`) không coi là chồng chéo.

### Phân tích thuật toán
- Thay vì nghĩ cách xóa sao cho tối thiểu, bài toán tương đương với: **Tìm số lượng tối đa các khoảng không chồng chéo**. Đáp án sẽ là `Tổng số - Số khoảng tối đa lấy được`.
- **Greedy choice:** Ưu tiên chọn các khoảng kết thúc sớm nhất để chừa diện tích rộng lớn nhất cho các khoảng sau. Sắp xếp mảng theo điểm kết thúc. Duyệt mảng và chọn các khoảng không bị đè (điểm bắt đầu của khoảng sau $\ge$ điểm kết thúc khoảng trước). Đếm số khoảng bị đè (vi phạm) - đó chính là số lần phải xóa.

### Mã nguồn Java
```java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        if (intervals.length == 0) return 0;
        
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[1], b[1]));
        
        int end = intervals[0][1];
        int removedCount = 0;
        
        for (int i = 1; i < intervals.length; i++) {
            // Khoảng này đè lên khoảng ta đã lấy -> cần xóa
            if (intervals[i][0] < end) {
                removedCount++;
            } else {
                // Không đè lên, ta mở rộng biên độ kết thúc
                end = intervals[i][1];
            }
        }
        
        return removedCount;
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N \log N)$ (do sắp xếp).
- **Không gian bộ nhớ (Space Complexity):** $O(1)$.

---

## 30. Reorganize String (LeetCode 767)
### Đề bài chi tiết
Cho chuỗi `s`. Hãy sắp xếp lại các ký tự trong chuỗi sao cho không có 2 ký tự nào nằm kề nhau mà giống nhau. Trả về kết quả, hoặc `""` nếu không thể tạo ra chuỗi nào thỏa mãn.

### Phân tích thuật toán
- Điều kiện bất khả thi: Nếu một ký tự chiếm tới quá một nửa số ô trong chuỗi (`maxFreq > (N + 1) / 2`), nó chắc chắn sẽ bị đứng cạnh nhau, lúc này trả về `""`.
- **Greedy choice:** Để dễ dàng chèn ký tự nhất, ta điền ký tự xuất hiện nhiều nhất (max freq) vào các vị trí chẵn `(0, 2, 4...)`. Việc làm đầy các chỗ trống cách quãng này triệt tiêu nguy cơ ký tự bị kề nhau.
- Sau khi điền hết ký tự "khó nhằn" đó, ta rải rác các ký tự còn lại vào các khe trống đang còn ở vị trí chẵn. Nếu vị trí chẵn cạn, ta lấp lại từ đầu ở các vị trí lẻ `(1, 3, 5...)`.

### Mã nguồn Java
```java
class Solution {
    public String reorganizeString(String s) {
        int[] hash = new int[26];
        for (int i = 0; i < s.length(); i++) {
            hash[s.charAt(i) - 'a']++;
        } 
        
        int maxFreq = 0, letter = 0;
        for (int i = 0; i < hash.length; i++) {
            if (hash[i] > maxFreq) {
                maxFreq = hash[i];
                letter = i;
            }
        }
        
        // Nếu quá bán chiều dài
        if (maxFreq > (s.length() + 1) / 2) {
            return ""; 
        }
        
        char[] res = new char[s.length()];
        int idx = 0;
        
        // Điền ký tự maxFreq vào các vị trí chẵn
        while (hash[letter] > 0) {
            res[idx] = (char) (letter + 'a');
            idx += 2;
            hash[letter]--;
        }
        
        // Điền các ký tự còn lại
        for (int i = 0; i < hash.length; i++) {
            while (hash[i] > 0) {
                // Nếu vượt quá mảng (hết chỗ chẵn), vòng về chỗ lẻ đầu tiên
                if (idx >= res.length) {
                    idx = 1; 
                }
                res[idx] = (char) (i + 'a');
                idx += 2;
                hash[i]--;
            }
        }
        
        return String.valueOf(res);
    }
}
```

### Độ phức tạp
- **Thời gian (Time Complexity):** $O(N)$ (duyệt mảng 26 ký tự O(1) và một vòng điền kết quả vào mảng độ dài $N$).
- **Không gian bộ nhớ (Space Complexity):** $O(N)$ (để chứa chuỗi kết quả) hoặc $O(1)$ nếu chỉ tính bộ nhớ lưu trữ `hash`.
